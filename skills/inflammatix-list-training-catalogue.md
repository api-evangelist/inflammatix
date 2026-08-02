---
name: List the TriVerity and Myrna training catalogue
description: >-
  Enumerate the Inflammatix operator-training courses, lessons and quizzes for the
  TriVerity Test System and the Myrna instrument from the support portal's WordPress
  REST API, and know exactly where the customer login wall starts.
api: openapi/inflammatix-support-content-openapi.yml
base_url: https://support.inflammatix.com/wp-json/wp/v2
operations:
  - listCourses
  - getCourse
  - listLessons
  - getLesson
  - listQuizzes
  - getQuiz
  - listCourseCategories
  - listCourseTags
  - listSearch
generated: '2026-08-01'
method: generated
source: >-
  Grounded in openapi/inflammatix-support-content-openapi.yml, which is derived
  verbatim from https://support.inflammatix.com/wp-json/. Every operationId below
  exists in that contract.
---

# List the TriVerity and Myrna training catalogue

`support.inflammatix.com` is the Inflammatix customer support and operator-training
portal, running MemberPress Courses. The course **catalogue** is anonymously
readable through the WordPress REST API; the course **content** is not.

## Before you start

- **No authentication for the catalogue.** Course, lesson and quiz collections return
  `200` anonymously (verified 2026-08-01).
- **The login wall is inside the record, not in front of it.** Anonymous callers get
  `id`, `slug`, `title`, `date`, `link` and taxonomy terms; `content.rendered` is
  empty or truncated for gated lessons. Treat a missing body as "gated", not as
  "empty course".
- The customer login is at <https://support.inflammatix.com/login/>. It is a
  MemberPress member session, **not** an API credential — it will not authenticate REST
  calls. The only API credential this host advertises is a WordPress Application
  Password, which is an administrative mechanism, not a customer one.
- **Not clinical guidance.** Course titles describe operator training for a regulated
  IVD. Do not paraphrase them as instructions for use.

## Steps

### 1. Read the course taxonomy

Call `listCourseCategories` (`GET /wp/v2/mpcs-course-categories?per_page=100`) and
`listCourseTags` (`GET /wp/v2/mpcs-course-tags?per_page=100`). Keep the term ids.

### 2. Enumerate courses

Call `listCourses` (`GET /wp/v2/mpcs-course`) with `per_page=100`,
`orderby=date&order=desc`, and optionally `mpcs-course-categories=<term id>` or
`search=<terms>`. Read `X-WP-Total` / `X-WP-TotalPages` for the page count.

### 3. Enumerate lessons and quizzes

Call `listLessons` (`GET /wp/v2/mpcs-lesson`) and `listQuizzes`
(`GET /wp/v2/mpcs-quiz`) the same way.

**Important structural caveat:** the `wp/v2` collections do not carry a public
parent-course field, so you cannot rebuild curriculum order from this namespace alone.
The plugin exposes curriculum membership at `/mpcs/courses/curriculum/{id}` in its own
namespace, which is not part of the derived contract and is permission-gated. Record
lessons as a flat set unless you have authenticated access; do not infer an ordering.

### 4. Fetch individual records

`getCourse`, `getLesson`, `getQuiz` (`GET /wp/v2/mpcs-course/{id}` and siblings) with
`_embed` to inline terms and featured media. Expect an empty `content.rendered` on
gated items.

### 5. Search across the portal

Call `listSearch` (`GET /wp/v2/search?search=<terms>`) to find support pages and
courses together; the `subtype` field tells you which post type each hit belongs to.

## Handling errors

WordPress envelope `{code, message, data.status}` — see
`errors/inflammatix-problem-types.yml`. Watch for `rest_forbidden` (401) if you request
`context=edit`, `rest_post_invalid_id` (404), `rest_invalid_param` (400 on
`per_page > 100`), and undocumented `429` throttling.

## Notes

- The route sets on `inflammatix.com` and `support.inflammatix.com` are **not**
  identical — the support host adds the `mpcs-*` types, the corporate host adds
  `publication`. A route that works on one returns `404 rest_no_route` on the other.
- Cross-cutting semantics: `conventions/inflammatix-conventions.yml`.
