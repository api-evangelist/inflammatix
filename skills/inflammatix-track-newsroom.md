---
name: Track the Inflammatix newsroom
description: >-
  Pull Inflammatix press releases, news coverage and machine-learning blog posts as
  structured JSON, filtered by date, category or tag, for monitoring regulatory and
  commercial milestones.
api: openapi/inflammatix-content-openapi.yml
base_url: https://inflammatix.com/wp-json/wp/v2
operations:
  - listPosts
  - getPost
  - listCategories
  - getCategory
  - listTags
  - listMedia
  - getMediaItem
generated: '2026-08-01'
method: generated
source: >-
  Grounded in openapi/inflammatix-content-openapi.yml, which is derived verbatim from
  https://inflammatix.com/wp-json/. Every operationId below exists in that contract.
---

# Track the Inflammatix newsroom

Inflammatix has no changelog, no status page and no API release notes. Its newsroom is
the only published change stream, and it carries the milestones that matter — FDA
clearance, BARDA/DARPA awards, financing rounds, shipments to the JPEO-CBRND. This
skill reads that stream as JSON rather than scraping
<https://inflammatix.com/newsroom/news/>.

## Before you start

- **No authentication**, anonymous reads only.
- **Rate limit unknown.** Cloudflare returns `429` on rapid bursts with no
  `Retry-After`. One request at a time, exponential back-off on `429`.
- There were 117 posts as of 2026-08-01 (`X-WP-Total`), so a full sweep is two pages
  at `per_page=100`.

## Steps

### 1. Map the categories

Call `listCategories` (`GET /wp/v2/categories?per_page=100`). This separates press
releases from news coverage from ML blog entries. Keep the term `id` values.

### 2. Sweep or poll the posts

Call `listPosts` (`GET /wp/v2/posts`):

- First sweep: `per_page=100&page=1`, then `page=2`, ordered `orderby=date&order=desc`.
  Read `X-WP-TotalPages` and stop there.
- Incremental poll: `after=<ISO 8601 timestamp of your last run>` returns only what
  is new. Use `modified_after` instead if you also want edits to existing posts.
- Scope with `categories=<term id>` or `tags=<term id>`.
- Use `_fields=id,date,modified,slug,title,excerpt,link,categories` while polling;
  fetch full bodies only for the items you actually care about.

### 3. Fetch the full item

Call `getPost` (`GET /wp/v2/posts/{id}?_embed`). `_embed` inlines the categories,
tags and featured media so you do not have to walk `_links`.

Note that many newsroom items are coverage — the `link` field can point off-site (a
broadcast or trade publication), while `content.rendered` holds the Inflammatix-hosted
version. Check `link` before treating it as a first-party URL.

### 4. Pull assets when needed

Call `listMedia` / `getMediaItem` (`GET /wp/v2/media`, `GET /wp/v2/media/{id}`) for
images and PDFs. This is also where the published compliance artefacts live — the BSI
ISO 13485:2016 certificate and the State of California device manufacturing licence
are media attachments linked from the about page.

## Handling errors

WordPress envelope `{code, message, data.status}`, not RFC 9457. See
`errors/inflammatix-problem-types.yml`. The ones you will hit polling:
`rest_invalid_param` (400, usually a malformed `after` timestamp — it must be full
ISO 8601, e.g. `2026-01-01T00:00:00`), `rest_post_invalid_page_number` (400), and
`429`.

## Notes

- Feed alternative: `https://inflammatix.com/feed/` returns the same stream as RSS if
  you want a lighter poll, but without category ids or `modified` timestamps.
- Company lifecycle context — regulatory milestones, product pipeline, and the absence
  of any status page or deprecation policy — is in
  `lifecycle/inflammatix-lifecycle.yml`.
