# Inflammatix

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
