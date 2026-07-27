---
name: Track and manage Goody orders
description: Look up order batches and individual orders, read their activity/status, cancel unaccepted gifts, and extend expirations.
api: openapi/goody-api-openapi-original.json
operations:
  - GET /v1/order_batches
  - GET /v1/order_batches/{id}/orders
  - GET /v1/orders
  - GET /v1/orders/{id}
  - GET /v1/order_activities
  - POST /v1/orders/{id}/cancel
  - POST /v1/orders/{id}/update_expiration
---

# Track and manage Goody orders

Authenticate with `Authorization: Bearer <GOODY_API_KEY>`.

## Steps

1. **List batches** — `GET /v1/order_batches` (filter with
   `created_at[after]` / `created_at[before]`, `statuses`).
2. **Drill into a batch** — `GET /v1/order_batches/{id}/orders` for the
   per-recipient orders, or `GET /v1/order_batches/{id}` for the batch itself.
3. **Inspect an order** — `GET /v1/orders/{id}`; use `GET /v1/order_activities`
   for the status/activity trail.
4. **Cancel** — `POST /v1/orders/{id}/cancel` while a gift is still unaccepted.
5. **Extend** — `POST /v1/orders/{id}/update_expiration` to move an
   unaccepted gift's expiry.

## Rules
- Cancellation only works before the recipient accepts; check status first.
- Prefer webhooks (order.shipped, order.delivered, order.canceled,
  order.refunded) over polling for near-real-time state.
- Errors are `{ "error": "<message>" }`.
