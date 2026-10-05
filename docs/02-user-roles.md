# 02 — User Roles

Bookstore has one public audience and four account roles. Internal roles are **strictly separated**: each one covers its own area of the business, and no role holds every permission.

## Personas

| Role | Who | Main goal |
| :--- | :--- | :--- |
| **Guest** | Anonymous visitor | Browse and evaluate books |
| **Customer** | Registered shopper | Buy books and track orders |
| **Staff** | Sales and warehouse employee | Keep the catalog and stock accurate, fulfil orders |
| **Admin** | Business manager | Monitor performance, payments, and customers |
| **Super Admin** | System owner | Manage internal accounts and review access rights |

## Capability Matrix

| Capability | Guest | Customer | Staff | Admin | Super Admin |
| :--- | :-: | :-: | :-: | :-: | :-: |
| Browse & search catalog | ✅ | ✅ | ✅ | ✅ | ✅ |
| Cart, wishlist, checkout | | ✅ | | | |
| Write reviews | | ✅ | | | |
| Manage books & covers | | | ✅ | | |
| Feature books on the home page | | | | ✅ | |
| Import stock, view inventory | | | ✅ | | |
| Process orders (status updates) | | | ✅ | | |
| View orders | | own | ✅ | ✅ | |
| View all payments | | | | ✅ | |
| Dashboard | | | ✅ | ✅ | |
| Moderate reviews | | | | ✅ | |
| Lock / unlock customers | | | | ✅ | |
| Create / lock staff & admin accounts | | | | | ✅ |
| View roles & permissions | | | | | ✅ |

## Access Model

- Access is granted through **permissions** in the form `RESOURCE:ACTION` (for example `BOOK:UPDATE` or `ORDER:VIEW`), which are grouped into **roles**.
- The server checks permissions on every request. The interface hides actions the user cannot perform.
- Locking an account takes effect **immediately**: the user's active sessions are revoked.
- Customers only ever see their own data (orders, addresses, payments, reviews).
