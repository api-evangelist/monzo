---
name: List, retrieve and annotate transactions
description: Page through a Monzo account's transactions, expand merchant detail, and attach custom metadata.
api: https://docs.monzo.com
operations:
  - GET /accounts
  - GET /transactions
  - GET /transactions/{transaction_id}
  - PATCH /transactions/{transaction_id}
---

# List, retrieve and annotate transactions

Use this skill to read and enrich a Monzo user's transactions.

## Prerequisites
- OAuth 2.0 Bearer access token on every request (`Authorization: Bearer {access_token}`).
- Strong Customer Authentication is required for access to transactions older than 90 days; that access lapses five minutes after authentication unless re-approved.

## Steps
1. **Find the account** — `GET /accounts` and pick the `account_id`.
2. **List transactions** — `GET /transactions?account_id={account_id}` with pagination: `limit` (default 30, max 100), `since` (RFC 3339 timestamp or object id), `before` (RFC 3339 timestamp).
3. **Expand merchant** — add `expand[]=merchant` to inline merchant details.
4. **Retrieve one** — `GET /transactions/{transaction_id}` for a single transaction.
5. **Annotate** — `PATCH /transactions/{transaction_id}` with `metadata[key]=value` form params. Send an empty value to delete a key.

## Rules
- Pagination and expansion conventions live in `conventions/monzo-conventions.yml`.
- Handle `401` (invalid/expired token) and `429` (rate limited) per `errors/monzo-problem-types.yml`.
