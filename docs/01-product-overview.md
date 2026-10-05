# 01 — Product Overview

## Vision

Give readers a fast, trustworthy way to find and buy books online, and give the shop team one place to run the catalog, stock, orders, and payments.

## Problems Addressed

| For | Problem | How Bookstore helps |
| :--- | :--- | :--- |
| Customers | Hard to find the right book or the next volume of a series | Search with filters, tags, and series pages that list volumes in order |
| Customers | Unsure about stock and payment safety | Live stock status, a cart that warns about unavailable items, secure VNPay checkout |
| Staff | Stock spread across spreadsheets | Stock imports and a complete movement history per book |
| Managers | No clear view of business performance | Dashboard with revenue, orders, customers, and low-stock alerts |
| Owners | Too many people with too much access | Strict role separation with fine-grained permissions |

## Scope

### In Scope
- Catalog: books, categories, tags, series, featured books
- Customer accounts: profile, multiple addresses, deactivate and reactivate
- Shopping: cart, wishlist, reviews with helpful votes
- Ordering: checkout with Cash on Delivery or VNPay, order tracking, cancellation
- Back office: catalog management, inventory, order fulfilment, payment monitoring, user and role administration, dashboard

### Out of Scope (Current Version)
- Vouchers and promotions (the data model is prepared, but there is no UI yet)
- Shipping-carrier integration and automatic shipping fees
- Refund automation
- Native mobile apps

## Feature Map

| Area | Features |
| :--- | :--- |
| **Discovery** | Keyword search, category / tag / price filters, sorting (newest, price, best-selling, top-rated), featured books, series browsing |
| **Book page** | Details, stock status, series navigation, ratings and reviews |
| **Account** | Registration, login, profile, password change, addresses with a default, account deactivation |
| **Shopping** | Cart with stock warnings, wishlist |
| **Checkout** | Address selection, COD or VNPay, protection against duplicate orders |
| **After purchase** | Order history, order detail, cancellation, repeat payment for unpaid orders, review purchased books |
| **Back office** | Dashboard, books, inventory, orders, payments, users, roles |
