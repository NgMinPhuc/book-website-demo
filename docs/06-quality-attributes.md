# 06 — Quality Attributes

How the system meets its non-functional requirements. Implementation specifics are in the [Backend](../BE/README.md) and [Frontend](../FE/README.md) docs.

## Security

| Concern | Approach |
| :--- | :--- |
| Authentication | Signed, short-lived access tokens with refresh tokens |
| Session control | Server-side session registry: logout, password change, and account lock take effect instantly |
| Authorization | Role-based access with fine-grained permissions, enforced on the server for every request |
| Data isolation | Customers can only reach their own records |
| Passwords | Stored as strong one-way hashes, never logged or returned |
| Payments | Gateway messages are signature-verified and amount-checked |
| Uploads | File type and size validated before storage |
| Secrets | Supplied through the environment, never stored in code |

## Data Consistency

- Checkout is **atomic**: stock, order, history, and cart change together or not at all.
- Overselling is prevented at the database level, even when many customers buy at once.
- **Idempotent** order submission and payment notifications: retries never duplicate data.
- Database constraints protect key invariants (non-negative stock, correct totals, a single successful payment).

## Performance

- Frequently used operations are designed to use only a few database round-trips.
- Targeted indexes cover every storefront sort order, plus fuzzy title and author search.
- Permission checks need no database access per request.
- Images are served directly from object storage.
- The web app loads each page on demand (code splitting).

## Reliability & Operability

- Containerised services with persistent volumes.
- Versioned, automatic database migrations.
- A health endpoint covers the database, cache, and storage.
- Structured logs record business events (orders, payments, state changes) without personal data.
- Consistent error responses with domain error codes.

## Maintainability & Testability

- Clear layering on the server and clear module boundaries on the client.
- Typed contracts end to end, documented with OpenAPI.
- Automated tests at three levels: unit, API slice, and integration against real database and cache containers, including concurrency tests for checkout.
