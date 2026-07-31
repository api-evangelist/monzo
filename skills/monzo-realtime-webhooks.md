---
name: Receive real-time transaction notifications via webhooks
description: Register, list and delete Monzo webhooks to receive transaction.created events in real time.
api: https://docs.monzo.com
operations:
  - POST /webhooks
  - GET /webhooks
  - DELETE /webhooks/{webhook_id}
---

# Receive real-time transaction notifications via webhooks

Use this skill to subscribe to a Monzo account's real-time events.

## Prerequisites
- OAuth 2.0 Bearer access token on every request.
- A publicly reachable HTTPS endpoint to receive event POSTs.
- The target `account_id` (from `GET /accounts`).

## Steps
1. **Register** — `POST /webhooks` with `account_id` and `url` (your receiving endpoint).
2. **List** — `GET /webhooks?account_id={account_id}` to see registered webhooks.
3. **Delete** — `DELETE /webhooks/{webhook_id}` to remove one.

## Handling events
- Monzo POSTs a JSON body with a `type` and `data` envelope. The `transaction.created` event fires immediately when a new transaction is created; `data` is the full transaction object (with merchant when present).
- Delivery is retried up to 5 times with exponential backoff, so make your handler **idempotent** — deduplicate on the transaction id.
- See `asyncapi/monzo-webhooks.yml` for the event catalog.
