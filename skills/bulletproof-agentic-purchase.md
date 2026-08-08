---
name: Buy from the Bulletproof store as an agent
description: >-
  Search the Bulletproof catalog, build a cart, and take a checkout through to
  buyer-approved completion over the store's live UCP MCP endpoint.
api: mcp/bulletproof-mcp.yml
endpoint: https://shop.bulletproof.com/api/ucp/mcp
operations:
  - search_catalog
  - get_product
  - lookup_catalog
  - create_cart
  - update_cart
  - get_cart
  - create_checkout
  - update_checkout
  - complete_checkout
  - get_order
generated: '2026-08-08'
method: generated
source: mcp/bulletproof-ucp-mcp-tools.json (tools/list, fetched 2026-08-08)
---

# Buy from the Bulletproof store as an agent

Bulletproof runs a live Universal Commerce Protocol MCP server at
`https://shop.bulletproof.com/api/ucp/mcp`. Every tool name and argument below comes from
that endpoint's own `tools/list` response — see `mcp/bulletproof-ucp-mcp-tools.json` for
the full JSON Schema of each tool.

## Before you call anything

1. `initialize` the MCP session. The server answers as `universal-commerce` 0.1.0 on MCP
   protocol `2024-11-05`.
2. Every `tools/call` **must** carry `meta["ucp-agent"].profile` — a resolvable HTTPS agent
   profile URI. If the server cannot fetch it you get JSON-RPC `-32001`
   `UCP discovery failed` with `data.code` of `invalid_profile_url` or
   `profile_unreachable`, and nothing else will work.
3. Order and checkout tools additionally require a JWT. Without one the server returns
   JSON-RPC `-32000` `AuthenticationRequired`. `tools/list` and `initialize` are the only
   anonymous methods.
4. Pass `context.address_country` and `context.currency` so pricing and availability are
   correct. The store's own `agents.md` asks for this explicitly.

## Happy path

1. **Find something.** `search_catalog` takes a natural-language `query`, filters, or both —
   at least one is required. Results are cursor-paginated; follow `pagination.cursor` for
   more. If you already hold identifiers, use `lookup_catalog` (max 10 gids per request)
   instead.
2. **Confirm the variant.** `get_product` returns exact pricing and real-time availability
   and supports `selected` / `preferences` for option narrowing. Do this before adding to a
   cart — the search result is a summary, not a purchase decision.
3. **Build the cart.** `create_cart`, then `update_cart` for line, note, attribute, discount
   and buyer-identity changes. `get_cart` reads it back. `cancel_cart` discards it.
4. **Start the checkout.** `create_checkout`, then `update_checkout` to set the shipping
   address and delivery method. `get_checkout` returns line items, totals, discounts and
   taxes at any point.
5. **Complete it.** `complete_checkout` returns the order id and the thank-you page URL.

## The two rules that are not optional

**Human approval on payment.** The store's `agents.md` states it plainly: agents must not
complete payment without explicit, contemporaneous buyer consent. If you cannot get consent
at the moment of payment, do not call `complete_checkout` — route the purchase through the
Shop Pay skill the store points at instead.

**Idempotency on completion only.** `complete_checkout` accepts
`meta["idempotency-key"]`. Set it. It is the only idempotency control on the entire
Bulletproof surface — no other tool and no GraphQL mutation exposes one — so a retried
completion without it is a second order.

## Failure handling

- `-32001 UCP discovery failed` — your agent profile URI is missing or unreachable. Fix the
  profile, do not retry the call unchanged.
- `-32000 AuthenticationRequired` — mint a JWT before calling `get_order`,
  `create_checkout`, `update_checkout`, `complete_checkout` or `cancel_checkout`.
- `429` — the MCP endpoint is rate-limited per IP. Back off; `agents.md` says so directly.
- Payment failures come back as `CompletionErrorCode` values
  (`PAYMENT_CARD_DECLINED`, `PAYMENT_INSUFFICIENT_FUNDS`, `PAYMENT_CALL_ISSUER`, …). The
  full list is in `errors/bulletproof-decline-codes.yml`. Nothing here is RFC 9457 problem
  details.

## Related artifacts

- `mcp/bulletproof-mcp.yml` — server manifest and access model
- `mcp/bulletproof-tool-crosswalk.yml` — which GraphQL field backs each tool
- `conventions/bulletproof-conventions.yml` — pagination, tracing, idempotency, rate limits
- `errors/bulletproof-problem-types.yml` — the error envelopes
