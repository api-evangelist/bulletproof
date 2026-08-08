# BulletProof

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Bulletproof (Bulletproof 360, Inc.) is a Seattle-founded functional nutrition and
health-and-wellness brand — toxin-tested coffee, C8 MCT oil, collagen, creamers and
supplements — founded in 2013 by Dave Asprey and sold direct-to-consumer and through
national retail.

Bulletproof is not a software vendor and publishes no developer program, no API
reference and no `security.txt`. It nonetheless exposes a real, callable, largely
unauthenticated machine surface, all of it profiled here from public probes on
2026-08-08:

- **Storefront GraphQL API** — `https://shop.bulletproof.com/api/2026-01/graphql.json`
  answered anonymous introspection: 424 types, 36 root queries, 41 mutations. SDL in
  [`graphql/`](graphql/).
- **UCP agentic-commerce MCP endpoint** — `https://shop.bulletproof.com/api/ucp/mcp`
  returned 13 tools with full JSON Schema input contracts from an anonymous
  `tools/list`. Manifest and verbatim tool list in [`mcp/`](mcp/).
- **Agent instructions** — the store serves its own `/agents.md` and `/llms.txt`,
  including an explicit buyer-approval rule on checkout. Captured verbatim in
  [`skills/`](skills/) and [`llms/`](llms/).
- **OAuth 2.0 / OpenID Connect discovery** for customer accounts, plus an RFC 9728
  protected-resource document. Captured in [`well-known/`](well-known/).
- **WordPress REST API** behind the editorial site: 16 namespaces, 384 routes, no
  advertised authentication.

No A2A agent card was found on any host, so none was authored.

- Company: https://www.bulletproof.com/
- Store: https://shop.bulletproof.com/
- Secondary market: https://forgeglobal.com/bulletproof_stock/
