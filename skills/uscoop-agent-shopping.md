---
name: Shop the tnuck.com storefront as an agent (UCP/MCP)
description: Operate the store's published Universal Commerce Protocol shopping
  service over MCP - discover capabilities, search the catalog, build a cart,
  and check out with explicit buyer approval.
api: https://tnuck.com/api/ucp/mcp
operations:
- search_catalog
- create_cart
- create_checkout
- update_checkout
- complete_checkout
generated: '2026-07-21'
method: generated
source: https://tnuck.com/llms.txt
---

# Shop the tnuck.com storefront as an agent

This skill packages the agent flow the store itself publishes at
`https://tnuck.com/llms.txt` (mirroring `https://tnuck.com/agents.md`). All
tool names below are the store's own documented UCP shopping tools — none are
invented.

## Steps

1. **Discover** — `GET https://tnuck.com/.well-known/ucp` to confirm supported
   UCP versions (`2026-04-08` latest) and capabilities. The MCP endpoint is
   `POST https://tnuck.com/api/ucp/mcp` (`Content-Type: application/json`);
   use MCP `tools/list` to fetch tool schemas. Note: the endpoint requires an
   agent profile URI (probe on 2026-07-21 returned a structured
   `invalid_profile_url` error without one).
2. **Search** — call `search_catalog` with the buyer's intent. Pass
   `context.address_country` and `context.currency` for accurate pricing and
   availability.
3. **Cart** — call `create_cart` to add the chosen items.
4. **Checkout** — call `create_checkout` to start the purchase flow.
5. **Fulfill** — call `update_checkout` to set shipping address and method
   (single-destination shipping only, per the UCP profile).
6. **Complete** — call `complete_checkout` to finalize. **Checkout requires
   human approval**: never complete payment without explicit, contemporaneous
   buyer consent. If you cannot obtain it, the store recommends routing the
   purchase through the cross-store Shop skill
   (`https://shop.app/SKILL.md`) via Shop Pay instead.

## Conventions and limits

- The MCP endpoint is rate-limited per IP; back off on HTTP 429.
- Read-only browsing needs no authentication: `GET /products/{handle}.json`,
  `GET /collections/{handle}/products.json`, `GET /search?q={query}&type=product`.
- Customer-account auth is Shopify OIDC/OAuth2 — see
  `authentication/uscoop-authentication.yml` and `scopes/uscoop-scopes.yml`.
- Store policies: privacy `https://tnuck.com/policies/privacy-policy`, terms
  `https://tnuck.com/policies/terms-of-service`, refunds
  `https://tnuck.com/policies/refund-policy`.
