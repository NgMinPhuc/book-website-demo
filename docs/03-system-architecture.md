# 03 — System Architecture

## Context

```mermaid
flowchart LR
    Customer((Customer)) --> Web
    Team((Staff / Admin)) --> Web
    Web[Web App<br/>React SPA] -->|REST / JSON| API[Bookstore API<br/>Spring Boot]
    API --> DB[(PostgreSQL)]
    API --> Cache[(Redis)]
    API --> Store[(MinIO<br/>object storage)]
    API <-->|redirect + webhook| VNPay[VNPay<br/>payment gateway]
    Web -->|images| Store
```

## Components

| Component | Responsibility | Technology |
| :--- | :--- | :--- |
| **Web App** | Storefront and back-office UI in one single-page app with role-based navigation | React 19, TypeScript, Vite, Tailwind CSS |
| **Bookstore API** | Business logic, security, data access, integrations | Java 17, Spring Boot 3.4, Spring Security, JPA |
| **Database** | System of record: users, catalog, orders, payments, inventory, reviews | PostgreSQL 16, Flyway migrations |
| **Cache / session store** | Active sessions and lightweight counters | Redis 7 |
| **Object storage** | Book covers and avatars, served directly to browsers | MinIO (local) / Amazon S3 (AWS production) |
| **Payment gateway** | Online card and bank payments | VNPay |
| **Cloud Infrastructure** | Cloud hosting, identity, and object storage | AWS (EC2, S3, IAM) |

## Architectural Style

- **Modular monolith.** One deployable API, organised by business domain (catalog, orders, payments, …), with a strict layered structure inside each domain. This keeps operations simple while keeping the code ready to be split later.
- **Stateless API.** Requests are authenticated by token, so API instances can be scaled horizontally. Session revocation is centralised in Redis.
- **Single source of truth.** Business state lives in PostgreSQL. Redis holds only data that can be rebuilt.
- **API-first.** The web app relies only on the documented REST contract (OpenAPI).

## Deployment View

```mermaid
flowchart TB
    subgraph AWS Cloud
        subgraph EC2 Host ["Amazon EC2 (Docker Compose)"]
            Nginx[Nginx SSL / Proxy]
            FE[Web App<br/>static files]
            BE[API container]
            PG[(PostgreSQL)]
            RD[(Redis)]
        end
        IAM[AWS IAM Role<br/>Instance Profile]
        S3[(Amazon S3 Bucket)]
    end

    User((Browser)) --> Nginx
    Nginx --> FE
    Nginx --> BE
    BE --> PG & RD
    BE -.->|Credential-free auth| IAM
    BE -->|Upload media| S3
    User -->|Direct download| S3
    VNPay -->|IPN webhook| Nginx
```

- Every service runs in containers, orchestrated with Docker Compose on an **Amazon EC2** virtual host.
- The API image is a multi-stage build that runs as a non-root user.
- In production, **Amazon S3** provides highly available media storage; **MinIO** is used for local offline development.
- **AWS IAM** instance profiles eliminate the need for hardcoded AWS secret keys on the server.
- Persistent data sits in named volumes backed by EBS.
- The database schema is versioned and applied automatically on startup.
- See [07 — AWS Deployment](07-aws-deployment.md) for full deployment architecture, IAM policies, and setup steps.

## Technology Choices

| Decision | Rationale |
| :--- | :--- |
| PostgreSQL | Strong constraints, transactions, JSONB snapshots, trigram search, all in one engine |
| Redis for sessions | Instant logout and account locking on top of stateless tokens |
| Object storage for images | Keeps binaries out of the database; browsers load images directly from S3 / MinIO |
| Amazon EC2 | Flexible, cost-efficient virtual compute hosting containerised services |
| AWS IAM Instance Profile | Keyless, rotated credential access from EC2 to S3 following the principle of least privilege |
| VNPay | Widely used payment gateway in Vietnam with a sandbox for development |
| One SPA for both interfaces | Shared components and types; role-based routing separates the audiences |
