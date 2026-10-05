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
| **Object storage** | Book covers and avatars, served directly to browsers | MinIO (S3-compatible) |
| **Payment gateway** | Online card and bank payments | VNPay |

## Architectural Style

- **Modular monolith.** One deployable API, organised by business domain (catalog, orders, payments, …), with a strict layered structure inside each domain. This keeps operations simple while keeping the code ready to be split later.
- **Stateless API.** Requests are authenticated by token, so API instances can be scaled horizontally. Session revocation is centralised in Redis.
- **Single source of truth.** Business state lives in PostgreSQL. Redis holds only data that can be rebuilt.
- **API-first.** The web app relies only on the documented REST contract (OpenAPI).

## Deployment View

```mermaid
flowchart TB
    subgraph Docker host
        FE[Web App<br/>static files]
        BE[API container]
        PG[(PostgreSQL)]
        RD[(Redis)]
        MN[(MinIO)]
    end
    User((Browser)) --> FE
    User --> BE
    User --> MN
    BE --> PG & RD & MN
    VNPay -->|IPN webhook| BE
```

- Every service runs in containers, orchestrated with Docker Compose.
- The API image is a multi-stage build that runs as a non-root user.
- Persistent data sits in named volumes.
- The database schema is versioned and applied automatically on startup.

## Technology Choices

| Decision | Rationale |
| :--- | :--- |
| PostgreSQL | Strong constraints, transactions, JSONB snapshots, trigram search, all in one engine |
| Redis for sessions | Instant logout and account locking on top of stateless tokens |
| Object storage for images | Keeps binaries out of the database; browsers load images directly |
| VNPay | Widely used payment gateway in Vietnam with a sandbox for development |
| One SPA for both interfaces | Shared components and types; role-based routing separates the audiences |
