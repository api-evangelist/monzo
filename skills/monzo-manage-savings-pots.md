---
name: Move money between an account and savings pots
description: List a Monzo user's savings pots and safely move money in and out using dedupe_id for idempotent transfers.
api: https://docs.monzo.com
operations:
  - GET /pots
  - PUT /pots/{pot_id}/deposit
  - PUT /pots/{pot_id}/withdraw
---

# Move money between an account and savings pots

Use this skill to manage a Monzo user's savings pots.

## Prerequisites
- An OAuth 2.0 access token (authorization code grant) obtained via `https://auth.monzo.com/` and `https://api.monzo.com/oauth2/token`.
- Send every request with `Authorization: Bearer {access_token}`.
- You need the user's current `account_id` (from `GET /accounts`).

## Steps
1. **List pots** — call `GET /pots?current_account_id={account_id}` to retrieve the pots and their `pot_id` and balances.
2. **Deposit** — call `PUT /pots/{pot_id}/deposit` with `source_account_id`, `amount` (minor units), and a unique `dedupe_id`.
3. **Withdraw** — call `PUT /pots/{pot_id}/withdraw` with `destination_account_id`, `amount`, and a unique `dedupe_id`.

## Rules
- **Idempotency:** `dedupe_id` is required on deposit and withdraw. Generate one unique value per logical transfer and reuse the SAME value on retries so a transfer is applied only once (see `conventions/monzo-conventions.yml`).
- Amounts are in minor units (pence).
- On `429` back off and retry with the same `dedupe_id`; on `401` the token is expired/invalid — refresh or re-authenticate (see `errors/monzo-problem-types.yml`).
