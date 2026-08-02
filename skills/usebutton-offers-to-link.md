---
name: Fetch offers and generate an attributed link
description: Render personalized Button offers to a Publisher user, then wrap the chosen Brand destination in a fully attributable Button link.
api: openapi/usebutton-offers-openapi.yml, openapi/usebutton-links-openapi.yml
operations: [offers, generate-a-link]
generated: '2026-07-21'
method: generated
---

# Fetch offers and generate an attributed link (Publisher flow)

1. **Authenticate** every call with HTTP Basic: your organization API key as the username, blank password (`-u YOUR_API_KEY:`). See `authentication/usebutton-authentication.yml`.
2. **Fetch offers** with `offers` (`POST https://pubapi.usebutton.com/v1/offers`). Send `user_id` (1-255 ASCII chars, same ID you pass to the Button SDK) and, when available, `device_ids` (IDFA/GAID) and `email_sha256s` (SHA-256 of the lowercased, trimmed email, 64-char hex). At most 10 combined identifiers. Respect the documented rate limit of 5,000 requests/minute; cache offers rather than calling per-render.
3. **Render offers** from `object.merchant_offers[]`: each has `merchant_id` and ranked `offers[]` with `rate_percent` or `rate_fixed` + `currency`, plus optional `display_params` (`category`, `offer_type`, `offer_tag`). Skip offers with `offer_type: Exclusion` (0% / non-commissionable).
4. **Generate the link** with `generate-a-link` (`POST https://api.usebutton.com/v1/links`). Body: `url` (the Brand destination) plus `experience.btn_pub_user` (your user ID — required for Loyalty Publishers to reward the right user) and `experience.btn_pub_ref` (your click/campaign reference, max 512 chars). Both values are echoed back in transaction webhooks.
5. **Handle errors** in the `meta`/`error` envelope: `401 Invalid api_key`, `403` with `error.type: NoMerchantApproval` (not approved for this merchant), `404` with `error.type: NoMerchantLinkSupport` (URL not a supported Brand). See `errors/usebutton-problem-types.yml`.
6. **Close the loop** by consuming transaction webhooks (`tx-pending` → `tx-validated`/`tx-declined`); verify `X-Button-Signature` (HMAC-SHA256 of the raw body) and dedupe on webhook `id`. See `asyncapi/usebutton-webhooks.yml`.
