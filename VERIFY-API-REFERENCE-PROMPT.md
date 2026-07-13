# Task: Verify ReadMe source vs DocumentationAI converted API-Reference pages

You are auditing a docs migration. Every API-Reference page on the destination site
should show the same human-readable **prose** the original ReadMe page shows. Many
converted pages lost their prose during migration. Find every such page.

Run from repo root: `/home/javeed/Documents/CAPILLARY`

## The two sides

**CONVERTED (destination, "DocumentationAI"):**
- Prose:      `docs-capillary-demo/reference/<slug>.mdx`
- Playground: `docs-capillary-demo/api-reference/<slug>.yaml`  (OpenAPI — renders params + request/response)
- Live URL:   https://capillary-demo.documentationai.com/reference/<slug>
- The canonical list of pages + their `<slug>` is in `docs-capillary-demo/documentation.json`
  (every leaf with a `"path": "reference/<slug>"`).

**SOURCE (original, "ReadMe"):** the raw markdown for the same page, first match wins:
1. `INFO/backfill/raw/<slug>.md`
2. `capillary-migrator/verseion-2-capillary-api-references/<slug>.md`
3. `INFO/docs.capillarytech.com/API Reference/<slug>.md`
- Live URL: https://docs.capillarytech.com/reference/<slug>

## How to match a converted page to its source

1. Try exact `<slug>.md` in the three source dirs above (in order).
2. If none exists, read the endpoint `METHOD /path` from the converted
   `docs-capillary-demo/api-reference/<slug>.yaml` (the `paths:` key), then grep the
   source dirs for a `.md` whose OpenAPI/endpoint path matches — the destination slug
   may differ (e.g. `validate-otp-2` ↔ `validate-otp`).
3. If still nothing local, fetch the live ReadMe page
   `https://docs.capillarytech.com/reference/<slug>` (try the destination slug; if 404,
   note it and move on).
4. If no source can be found anywhere, mark the page `NO-SOURCE` (do not guess).

## Heading levels — STRICT exact-match rule

Every heading in the converted `.mdx` MUST use the **exact same level** as the same
heading in the source ReadMe. If the source heading is `#`, the converted heading must
be `#`; if source is `##`, converted must be `##`. **No shifting or normalization.**

- The ONLY source heading allowed to disappear is the page-title `# <Title>` H1 — it
  moves into the frontmatter `title:` field, not the body.
- A common defect: the converter shifted every body heading one level deeper
  (source `#` → converted `##`, nested `##` → `###`, `#####` → `######`). Flag every
  such heading.
- When comparing, match headings by their text (case/whitespace-insensitive) and compare
  the `#`-count. Ignore fenced code blocks. Verdict for any level difference:
  `HEADING-LEVEL-MISMATCH` (list each heading as `'<text>' src=H<n> conv=H<m>`).

## What counts as a discrepancy

The playground (from the `.yaml`) already renders the endpoint **description, request
parameters, request body schema, and sample request/response**. It is therefore OK if
the converted `.mdx` omits *those specific things* — do NOT flag them as missing.

**DO flag** any of this ReadMe prose that is absent from the converted `.mdx`:
- `Resource Information` table (URI, HTTP method, auth, rate-limited, batch support)
- `Prerequisites`
- Explanatory / behavioral prose and notes (e.g. "Parameters marked with * are mandatory…")
- `Callout` / note / warning / info boxes
- Error-code tables and their explanations
- `Response parameters` description tables (the prose table, distinct from the schema)
- "How it works", supported-transitions, related-links, and any other narrative sections
- Images referenced in the source

Also **strongly flag** any converted `.mdx` whose body is effectively empty
(only frontmatter + at most a one-line description) while the source has real prose —
these render as "API playground only, no content". Compare byte counts if helpful:
`wc -c docs-capillary-demo/reference/<slug>.mdx`.

## Method (be thorough, do not hallucinate)

- Process pages in batches. For each page: read the source, read the converted `.mdx`,
  and diff the **prose sections** (ignore playground-covered content per above).
- Quote the EXACT source headings/paragraphs that are missing — never invent content.
- Prioritize the ~45 pages with no exact-slug source and the near-empty stubs; but
  cover all ~200 pages listed in `documentation.json`.
- You may launch parallel subagents, each owning a slice of the slug list, to speed this up.

## Output

Produce `docs-capillary-demo/VERIFICATION-REPORT.md` containing:

1. **Summary counts:** total pages, `OK`, `MISSING-CONTENT`, `EMPTY-STUB`, `NEEDS-REVIEW`, `NO-SOURCE`.
2. **A table**, one row per page:

   | slug | source file used (or live URL) | verdict | missing sections |
   |------|--------------------------------|---------|------------------|

   Verdicts:
   - `OK` — all source prose present in converted page
   - `MISSING-CONTENT` — source has prose sections absent from the converted `.mdx` (list them)
   - `EMPTY-STUB` — converted `.mdx` is frontmatter-only but source has real prose
   - `NEEDS-REVIEW` — matched but ambiguous (slug remap uncertain, formatting differs, etc.)
   - `NO-SOURCE` — no source found in any local dir or live URL

3. Under the table, for every non-`OK` page, a short bullet list of the exact missing
   headings/paragraphs (quoted from source), so a follow-up backfill can restore them.

Do not modify any `.mdx`, `.yaml`, or `documentation.json` — this is a read-only audit.
