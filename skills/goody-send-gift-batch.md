---
name: Send a Goody gift batch
description: Choose products, price them, and send a gift batch to one or many recipients via the Goody Commerce/Automation API.
api: openapi/goody-api-openapi-original.json
operations:
  - GET /v1/products
  - GET /v1/cards
  - GET /v1/payment_methods
  - POST /v1/order_batches/price
  - POST /v1/order_batches
  - GET /v1/order_batches/{id}
---

# Send a Goody gift batch

Authenticate every request with `Authorization: Bearer <GOODY_API_KEY>`. Use the
sandbox host `https://api.sandbox.ongoody.com` while testing (separate key from
production `https://api.ongoody.com`).

## Steps

1. **Browse products** — `GET /v1/products` (paginate with `after`/`before` or
   `page`/`per_page`). Pull a product/variant id for the cart. Optionally
   `GET /v1/cards` to pick a greeting card (`card_id`).
2. **Pick a payment method** — `GET /v1/payment_methods` and keep a
   `payment_method_id`.
3. **Price the batch (no charge)** — `POST /v1/order_batches/price` with the
   cart, recipients, and send method to preview subtotal, tax, shipping, total.
4. **Send** — `POST /v1/order_batches` with `from_name`, `message`,
   `recipients`, `cart`, `send_method`, `payment_method_id`, and (recommended)
   a `customer_reference_id` — a unique idempotent id from your own system so a
   retried call does not double-send. This charges the payment method.
5. **Confirm** — `GET /v1/order_batches/{id}` to read the created batch, its
   orders, and gift links.

## Rules
- `customer_reference_id` is the idempotency key — always set it on send.
- Errors come back as `{ "error": "<message>" }` (see errors/goody-problem-types.yml).
- Sending money is high-consequence: confirm intent with the human before step 4.
- Subscribe to webhooks (order.created, order.gift_accepted, order.delivered) to
  track lifecycle rather than polling.
