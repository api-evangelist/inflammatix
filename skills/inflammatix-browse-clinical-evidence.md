---
name: Browse the Inflammatix clinical evidence library
description: >-
  Search, filter and retrieve the peer-reviewed publications, abstracts and posters
  Inflammatix publishes about TriVerity and host-response diagnostics, using the
  company's own WordPress REST content API.
api: openapi/inflammatix-content-openapi.yml
base_url: https://inflammatix.com/wp-json/wp/v2
operations:
  - listPublications
  - getPublication
  - listPublicationCategories
  - getPublicationCategory
  - listTags
  - listSearch
generated: '2026-08-01'
method: generated
source: >-
  Grounded in openapi/inflammatix-content-openapi.yml, which is derived verbatim from
  https://inflammatix.com/wp-json/. Every operationId below exists in that contract.
---

# Browse the Inflammatix clinical evidence library

Inflammatix registers a custom `publication` post type on its corporate site and
exposes it through the standard WordPress REST namespace. That makes its evidence
library — the papers, abstracts and posters behind
<https://inflammatix.com/evidence/> — machine-readable without scraping.

## Before you start

- **No authentication.** Every operation here is anonymously readable. Do not send
  credentials.
- **Pace yourself.** Cloudflare sits in front of the site and will return `429` on a
  rapid burst, with no `Retry-After`. Keep concurrency at 1 and back off exponentially
  on `429`.
- **This is not a clinical API.** There is no patient, sample, order or result
  resource anywhere on this surface. Do not present anything retrieved here as a
  diagnostic result.

## Steps

### 1. Learn the classification first

Call `listPublicationCategories` (`GET /wp/v2/category-publication`) with
`per_page=100`. Each term returns `id`, `name`, `slug` and `count`. Hold the `id`
values — they are the filter keys for step 2.

Optionally call `listTags` (`GET /wp/v2/tags`) too: on this site the `post_tag`
taxonomy is bound to **both** `post` and `publication`, so tags cross the newsroom and
the evidence library.

### 2. Page the publication collection

Call `listPublications` (`GET /wp/v2/publication`) with:

- `per_page=100` — the maximum. Anything higher returns `400 rest_invalid_param`.
- `page=1`, then increment. Read `X-WP-TotalPages` from the response headers and stop
  there; overrunning returns `400 rest_post_invalid_page_number`.
- `orderby=date&order=desc` for newest first.
- `category-publication=<term id>` to scope to one category from step 1.
- `search=<terms>` for full-text, or `after=2025-01-01T00:00:00` for a date window.
- `_fields=id,slug,title,excerpt,date,link` to keep payloads small while you survey,
  then re-fetch the ones you want in full.

### 3. Retrieve a single record

Call `getPublication` (`GET /wp/v2/publication/{id}`) for the full `content`,
`excerpt`, taxonomy terms and `_links`. Add `_embed` to inline the terms and any
featured media in one round trip instead of following `_links` yourself.

A bad id returns `404 rest_post_invalid_id` — list first, then fetch.

### 4. Fall back to site-wide search

If a title is known but the post type is not, call `listSearch`
(`GET /wp/v2/search?search=<terms>`). It returns lightweight `{id, title, url,
type, subtype}` records across every public post type, so you can tell whether a
result is a `publication`, a newsroom `post` or a static `page` before fetching it.

## Handling errors

All errors use the WordPress envelope `{code, message, data.status}` — **not** RFC 9457
problem+json. The codes you will actually hit are catalogued in
`errors/inflammatix-problem-types.yml`:

| Code | Status | What to do |
|---|---|---|
| `rest_invalid_param` | 400 | Fix the parameter named in `data.params`; usually `per_page > 100`. |
| `rest_post_invalid_page_number` | 400 | Stop at `X-WP-TotalPages`. |
| `rest_post_invalid_id` | 404 | The id does not exist or is unpublished. |
| `rest_no_route` | 404 | Route not registered — re-read `/wp-json/`. |
| — | 429 | Back off. Undocumented Cloudflare throttle. |

## Notes

- There is no idempotency contract and no rate-limit header. See
  `conventions/inflammatix-conventions.yml`.
- The entity graph, including which taxonomies bind to which post types, is in
  `data-model/inflammatix-data-model.yml`.
