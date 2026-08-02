---
name: Reconcile commissions with the Billing API
description: Pull billing accounts and cursor-paginate transactions to reconcile the commissions a Publisher has earned (or a Brand owes).
api: openapi/usebutton-billing-openapi.yml
operations: [list-accounts, list-transactions, list-transactions-all]
generated: '2026-07-21'
method: generated
---

# Reconcile commissions with the Billing API

1. **Authenticate** with HTTP Basic: API key as username, blank password (`-u YOUR_API_KEY:`).
2. **List accounts** with `list-accounts` (`GET https://api.usebutton.com/v1/affiliation/accounts`). Your organization has one billing account per currency (`acc-*`, with `currency` and `organization`).
3. **Pull transactions** either per account with `list-transactions` (`GET /v1/affiliation/accounts/{account_id}/transactions`) or across all currencies with `list-transactions-all` (`GET /v1/affiliation/transactions`).
4. **Filter** with `start`/`end` (RFC 3339) and `time_field` (`created_date`, `modified_date`, or `attribution_date`; default `created_date`), plus optional `country` (ISO 3166 alpha-2) and `page_size` (default 50).
5. **Paginate** by following the `cursor` embedded in `meta.next` until it is `null`. Treat the cursor as opaque.
6. **Interpret amounts**: every financial field is an integer in the smallest currency unit; `order_total` is the order value, `amount` is your commission. Transaction `status` moves `pending` → `validated` (final, billable) or `declined`. Poll on `modified_date` to pick up adjustments to pending transactions.
7. **Cross-reference** webhooks: transactions arriving via webhook (`tx-*` events) carry the same `id` (`tx-*`), `btn_ref` (`srctok-*`), and `button_order_id` fields — use them as join keys. See `data-model/usebutton-data-model.yml`.
