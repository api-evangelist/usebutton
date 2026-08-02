---
name: Create attributed creator shortlinks
description: Look up partnered Brands and mint Button shortlinks (including Amazon shortlinks) that creators can share for attributed purchases.
api: openapi/usebutton-brands-openapi.yml, openapi/usebutton-shortlink-openapi.yml
operations: [list-brand-details, generate-a-brand-shortlink, generate-a-shortlink]
generated: '2026-07-21'
method: generated
---

# Create attributed creator shortlinks

1. **Authenticate** with HTTP Basic: API key as username, blank password (`-u YOUR_API_KEY:`).
2. **Look up Brands** with `list-brand-details` (`GET https://api.usebutton.com/v1/brands`). Use it ad hoc or during Brand onboarding — Button says NOT to call it in the critical path of routing a user. Check `is_live_ready`, `supported_hostnames`, and `terms_and_conditions` (reporting window, finalization window, coupon/gift-card commissioning, exclusions) before promoting a Brand.
3. **Mint a Brand shortlink** with `generate-a-brand-shortlink` (`POST https://api.usebutton.com/v1/shortlink/create/`). Body: `link_url` (product/page URL), `btn_pub_ref` (required tracking reference, max 512 chars), and optionally `btn_pub_user` (1-255 ASCII chars). Response gives `object.short_url` (e.g. `https://o.bttn.io/...`) and the underlying wrapped `url`.
4. **Mint an Amazon shortlink** with `generate-a-shortlink` (`POST https://api.usebutton.com/v1/shortlink/create`). Body: `link_url` (Amazon URL), `btn_pub_user` (required), optional `btn_pub_ref` (e.g. Amazon tracking ID). Returns an `amzlink.to` short URL. NOTE: your organization must be allowlisted by your Button representative first.
5. **Handle errors** from the `meta`/`error` envelope: `401 Invalid api_key`, `404 could not create shortlink`. See `errors/usebutton-problem-types.yml`.
6. **Attribute downstream**: `btn_pub_ref` and `btn_pub_user` flow through to reporting and transaction webhooks so each creator's purchases can be commissioned. See `asyncapi/usebutton-webhooks.yml`.
