# Task: Verify NO content was lost migrating ReadMe → Documentation.AI (API Reference)

You are auditing a docs migration for **content completeness**. Every piece of
human-readable content on the original ReadMe page must still be present on the
converted Documentation.AI page. Your job: find every converted page that is
**missing any source content**, and quote exactly what is missing. Do not guess,
do not hallucinate, do not "fix" anything — this is a READ-ONLY audit.

Run from repo root: `/home/javeed/Documents/CAPILLARY`

## The two sides

**CONVERTED (destination, "Documentation.AI"):**
- Prose page:  `docs-capillary-demo/reference/<slug>.mdx`
- Playground:  `docs-capillary-demo/api-reference/<slug>.yaml` (OpenAPI — renders the
  endpoint description, request parameters, request body schema, and sample
  request/response in an interactive panel next to the prose).
- Live URL:    `https://capillary-demo.documentationai.com/reference/<slug>`
- The canonical page list + `<slug>` is every leaf in
  `docs-capillary-demo/documentation.json` with `"path": "reference/<slug>"`.

**SOURCE (original, "ReadMe"):** the raw markdown for the same page — first match wins:
1. `INFO/backfill/raw/<slug>.md`
2. `capillary-migrator/verseion-2-capillary-api-references/<slug>.md`
3. `INFO/docs.capillarytech.com/API Reference/<slug>.md`
- Live URL: `https://docs.capillarytech.com/reference/<slug>` (append `.md` for raw markdown).

## Matching a converted page to its source
1. Try exact `<slug>.md` in the three source dirs above, in order.
2. If none exists, read `METHOD /path` from `docs-capillary-demo/api-reference/<slug>.yaml`
   (`paths:` key) and grep the source dirs for a `.md` whose endpoint path matches —
   the destination slug may differ (e.g. `validate-otp-2` ↔ `validate-otp`).
3. If still nothing local, fetch the live ReadMe `.md`
   (`https://docs.capillarytech.com/reference/<slug>.md`). If it 404s, mark `NO-SOURCE`.
4. If no source is found anywhere, mark `NO-SOURCE` (do not invent content).

## What is INTENTIONALLY omitted — do NOT flag these as missing
The migration deliberately drops content that is redundant with the playground or
was a duplicate. Treat the following as expected and correct:
- **Endpoint description / summary** — the playground renders it from the YAML. If the
  source's one-line intro description is absent from the `.mdx`, that is fine.
- **Request parameters, request body schema, sample request** — rendered by the playground.
- **Sample response JSON blocks** — rendered by the playground. (But a prose **Response
  parameters description table** IS required — see below.)
- **A trailing `# OpenAPI definition` section** (a giant JSON dump of the whole spec) —
  intentionally removed; never flag it.
- **A body heading that merely repeats the page title** (`# <Title>`) — intentionally
  stripped because the title comes from frontmatter.

## What MUST be present — FLAG if any is absent from the converted `.mdx`
Compare the source prose against the `.mdx` and flag anything missing:
- **Resource Information** table (URI, HTTP method, authentication, rate-limited, batch support).
- **Prerequisites** section.
- **Request URL** section (the `http:{ae-host}/...` mobile/web URLs).
- **Explanatory / behavioral prose and notes**, e.g. "Parameters marked with * are
  mandatory…", "A first-time user cannot directly validate…", rate-limit explanations.
- **Callouts / notes / warnings / info / tip / danger boxes** (source emoji blockquotes
  `> 📘 / 👍 / 🚧 / ❗️` or `<Callout>`), including their full text.
- **Error-code tables** and their explanations.
- **Response parameters description table** (the prose table of param → description —
  distinct from the schema in the playground). Every row must be present.
- **"How it works", supported-transitions, step lists, related-links**, and any other
  narrative section.
- **Bullet / numbered lists** — every item.
- **Images** referenced in the source (`![...]` or `<Image>` / `files.readme.io` links).
- **Inline links** to other docs (the destination may rewrite the URL to a relative path
  — that is fine — but the link and its anchor text must still exist).

Also **strongly flag** any converted `.mdx` that is effectively empty (frontmatter +
at most a one-line description) while the source has real prose — "playground only, no
content". `wc -c docs-capillary-demo/reference/<slug>.mdx` is a quick smell test.

## Method (be thorough, verify — do not assume)
- Process pages in batches; you may launch parallel subagents, each owning a slice of the
  slug list from `documentation.json`, to cover all ~200 pages.
- For each page: read the source and the converted `.mdx`. Enumerate the source's content
  blocks (headings, tables, callouts, lists, paragraphs, images, links). For each block,
  confirm the same content exists in the `.mdx` (ignoring the intentionally-omitted items
  above, and ignoring pure formatting differences — heading level, `*`→`-`/`•` bullet
  glyph, table style, whitespace). Content equivalence is about the *words/rows/links*,
  not the markup.
- When something is missing, quote the EXACT source heading/paragraph/row that is absent.
  Never paraphrase or invent.
- Prioritize: pages with no exact-slug source, near-empty stubs, and pages with large
  source-vs-converted size gaps — but cover every page in `documentation.json`.

## Output
Write `docs-capillary-demo/CONTENT-COMPLETENESS-REPORT.md` containing:

1. **Summary counts:** total pages, `COMPLETE`, `MISSING-CONTENT`, `EMPTY-STUB`,
   `NEEDS-REVIEW`, `NO-SOURCE`.
2. **A table**, one row per page:

   | slug | source used (path or live URL) | verdict | missing content (short) |
   |------|--------------------------------|---------|-------------------------|

   Verdicts:
   - `COMPLETE` — all required source content is present in the converted page.
   - `MISSING-CONTENT` — one or more required source sections/rows/notes/images are absent.
   - `EMPTY-STUB` — converted `.mdx` is frontmatter-only but the source has real prose.
   - `NEEDS-REVIEW` — matched but ambiguous (uncertain slug remap, unclear equivalence).
   - `NO-SOURCE` — no source found in any local dir or the live URL.
3. Under the table, for every non-`COMPLETE` page, a bullet list quoting the exact missing
   headings / paragraphs / table rows / image references (verbatim from source), so a
   follow-up backfill can restore them precisely.

Do NOT modify any `.mdx`, `.yaml`, or `documentation.json`. Report only.
