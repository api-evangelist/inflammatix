# Inflammatix

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

Inflammatix is a molecular diagnostics company in Sunnyvale, California that reads the
patient's own immune response to speed decisions in emergency and critical care. Its
FDA-cleared TriVerity Test System measures a 29-mRNA host-response panel from whole
blood on the benchtop Myrna instrument using RT-LAMP, returning three
machine-learning-derived scores — bacterial infection likelihood, viral infection
likelihood, and risk of severe illness within seven days — in about 30 minutes.

- Website: https://inflammatix.com/
- Support portal: https://support.inflammatix.com/
- Secondary market listing: https://forgeglobal.com/inflammatix_stock/

## API surface

**Inflammatix publishes no developer API for its clinical products.** There is no
patient, sample, order, result or instrument resource anywhere public. The TriVerity
page advertises that the Myrna instrument supports "multiple LIS connectivity options"
and remote notification, but no interface specification, conformance statement or
integration guide is published — whether that is HL7 v2, ASTM or a proprietary
interface is not disclosed. That undocumented instrument-to-LIS interface is the
company's single largest interoperability gap.

Contract discovery on 2026-08-01 probed `/openapi.json`, `/openapi.yaml`,
`/swagger.json`, `/v1/openapi.json`, `/api-docs`, `/docs`, `/redoc`, every
`/.well-known/` discovery path, `/llms.txt`, and the `api.`, `docs.`, `developer.`,
`portal.`, `status.`, `trust.` and `security.` subdomains. All missed.

What Inflammatix does serve is two live, anonymously readable **WordPress REST content
APIs**, both self-describing at `/wp-json/`:

| API | Base URL | Operations | Notable |
|---|---|---|---|
| Site Content | `https://inflammatix.com/wp-json/wp/v2` | 20 | `publication` custom post type — the peer-reviewed clinical evidence library, as JSON |
| Support & Training Content | `https://support.inflammatix.com/wp-json/wp/v2` | 26 | MemberPress Courses types backing the TriVerity/Myrna operator-training catalogue |

Both OpenAPI 3.1 contracts in `openapi/` are derived mechanically from the sites' own
route-discovery documents — every path, method and parameter is copied verbatim.

## Artifacts

- `openapi/` — two OpenAPI 3.1 contracts (derived from live `/wp-json/` discovery)
- `overlays/` — API Evangelist annotations, kept out of the harvested contracts
- `examples/` — real response payloads captured live 2026-08-01
- `authentication/` — WordPress Application Passwords (HTTP Basic); not required for reads
- `conventions/` — pagination, filtering, sparse fields, error envelope, caching, CORS
- `errors/` — the WordPress `{code, message, data}` error catalogue (not RFC 9457)
- `data-model/` — entity graph read from each site's own type and taxonomy registries
- `lifecycle/` — namespace-path versioning; no status page, deprecation policy or changelog
- `conformance/` — ISO 13485:2016, FDA clearance, California device licence; no SOC 2, OAuth or FHIR
- `security/` — domain security probe (TLS 1.3, no HSTS/DNSSEC/CAA, SPF present, DMARC quarantine)
- `well-known/` — the negative `/.well-known/` probe record
- `mcp/` — candidate tool design only; no MCP server exists, and no `MCPServer` pointer is wired
- `skills/` — three agent skills grounded in real operationIds
- `llms/` — generated `llms.txt`

## Not present

No developer portal, API reference, getting-started guide, SDK, CLI, or Postman
collection. No GitHub organisation and no packages on any registry. No MCP server, no
A2A agent card, no webhooks or AsyncAPI. No status page, SLA, changelog or deprecation
policy. No security.txt, vulnerability disclosure programme, bug bounty or trust centre.
