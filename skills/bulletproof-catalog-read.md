---
name: Read the Bulletproof catalog without credentials
description: >-
  Query products, variants, collections and store metadata from the Bulletproof
  Storefront GraphQL API and the storefront JSON endpoints — both anonymous.
api: graphql/bulletproof-storefront.graphql
endpoint: https://shop.bulletproof.com/api/2026-01/graphql.json
operations:
  - products
  - product
  - productByHandle
  - productRecommendations
  - collections
  - collection
  - collectionByHandle
  - search
  - predictiveSearch
  - shop
  - paymentSettings
  - localization
  - publicApiVersions
generated: '2026-08-08'
method: generated
source: graphql/bulletproof-storefront.graphql (introspected live 2026-08-08)
---

# Read the Bulletproof catalog without credentials

The Bulletproof Storefront GraphQL API at
`https://shop.bulletproof.com/api/{version}/graphql.json` answered anonymous introspection
and anonymous data queries on 2026-08-08 — no `X-Shopify-Storefront-Access-Token`, no
cookie, `access-control-allow-origin: *`. Every field name below exists in
`graphql/bulletproof-storefront.graphql`.

## Pick a version

The path segment is a date. `publicApiVersions` returns the calendar live: `2025-10`,
`2026-01`, `2026-04` and `2026-07` are supported; `2026-10` is a release candidate and
`unstable` is not supported. An unknown segment returns HTTP **404**, not a GraphQL error.
The response echoes what actually served you in `x-shopify-api-version`.

## Reading products

- `products(first:, sortKey:, query:)` — Relay connection. `sortKey` accepts
  `BEST_SELLING`, `PRICE`, `CREATED_AT`, `RELEVANCE` and friends.
- `product(id:)` / `productByHandle(handle:)` — a single product. `handle` is the URL slug.
- On `Product`: `variants`, `selectedOrFirstAvailableVariant`,
  `variantBySelectedOptions`, `priceRange`, `compareAtPriceRange`, `totalInventory`,
  `availableForSale`, `sellingPlanGroups` (subscriptions), `metafields`, `seo`.
- `productRecommendations(productId:)` for cross-sell; `productTypes` and `productTags` for
  the store's own facets.

## Search

- `search(query:, types:)` for the general index.
- `predictiveSearch(query:)` for typeahead.

## Store metadata

`shop`, `paymentSettings` (currency, country, accepted card brands), `localization`,
`locations`, `menu`, `sitemap`, `urlRedirects`.

## Content

The store's blog content is reachable as `blogs`, `blog`, `blogByHandle`, `articles`,
`article`, `pages`, `page`, `pageByHandle`. The larger editorial library lives on a
different host — `https://www.bulletproof.com/wp-json/wp/v2` — a 384-route WordPress REST
API that advertises no authentication for its public read routes.

## Pagination and cost

Relay cursors: `first`/`last` with `after`/`before`, and read
`pageInfo.hasNextPage` / `endCursor`. There is no page-size ceiling published by
Bulletproof. Cost is metered, not counted: every response carries
`extensions.cost.requestedQueryCost` and `shopify-complexity-score` /
`shopify-complexity-score-v2` headers. No numeric rate-limit ceiling is published, so treat
the complexity score as the signal and back off on 429.

## The no-GraphQL path

If you only need the catalog, the store's own `agents.md` documents plain JSON endpoints
that need no query language and no credentials:

- `GET /products.json` and `GET /products/{handle}.json`
- `GET /collections.json` and `GET /collections/{handle}/products.json`
- `GET /search?q={query}&type=product`
- `GET /sitemap.xml`

## Errors

Validation failures come back as HTTP **200** with a populated `errors[]` array and an
`extensions.code` such as `undefinedField`. Mutation failures come back inside `data` as
`userErrors[]` / `cartUserErrors[]` with `{code, field, message}`. See
`errors/bulletproof-problem-types.yml` for the full enum inventory.

## Related artifacts

- `data-model/bulletproof-data-model.yml` — 34 entities, 77 relationships from this schema
- `conventions/bulletproof-conventions.yml` — identifiers, pagination, tracing, versioning
- `lifecycle/bulletproof-lifecycle.yml` — the version calendar and the 54 deprecated fields
