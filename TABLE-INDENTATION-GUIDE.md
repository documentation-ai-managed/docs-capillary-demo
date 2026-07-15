# Parameter Table Indentation — Issue Report & Convention Guide

## Summary

Nested-field indentation in Markdown parameter tables (the `•` / `◦` bullets)
**only renders if the leading spaces are em-spaces (`U+2003`), not ordinary
spaces (`0x20`).** Markdown strips ordinary leading spaces from table cells, so
any indent built with the spacebar disappears at render time.

This was observed in `reference/put-data-field-api.mdx`: two tables showed no
indentation while a third (copied from `reference/create-data-field.mdx`) did.
The working table used em-spaces; the others used ordinary spaces.

---

## The problem in detail

Markdown/MDX renderers **trim leading and trailing whitespace inside every
table cell**. A cell written as:

```
|   • fieldId | String | ... |
```

...where the indent is ordinary spaces, renders flush-left as `• fieldId` — the
spaces are discarded.

To force a visible indent you must use a **non-collapsing Unicode space**. The
convention in these docs is the **em-space, `U+2003`** (a wide space the
renderer preserves).

### Byte-level comparison

| File / row | Leading bytes | Space type | Renders indented? |
| --- | --- | --- | --- |
| `create-data-field.mdx` — `• fieldId` | `7c 20` `e28083 e28083` `e280a2` | `\| ` + 2× em-space + `•` | Yes ✅ |
| `put-data-field-api.mdx` (before fix) — `• fieldId` | `7c 20 20 20` `e280a2` | `\| ` + 3× ASCII space + `•` | No ❌ |

`e2 80 83` = `U+2003` em-space · `e2 80 a2` = `U+2022` `•` · `e2 97 a6` = `U+25E6` `◦`

---

## The convention to follow

Each nesting level is shown by a **marker**, preceded by a fixed number of
**em-spaces (`U+2003`)**. Both nested markers use the **same 2-em-space indent**
— depth is conveyed by the marker (`•` vs `◦`), not by extra indentation. This
matches the production docs.capillarytech.com rendering, where the deeper field
sits "outside" at the sibling level rather than pushed further right.

| Level | Meaning | Marker | Indent (em-spaces) | Cell looks like |
| --- | --- | --- | --- | --- |
| 0 | Top-level field | *(none)* | 0 | `\| fieldName \| ... \|` |
| 1 | Child (`•`) | `•` U+2022 | **2** | `\| ⟨em⟩⟨em⟩• fieldName \| ... \|` |
| 2 | Grandchild (`◦`) | `◦` U+25E6 | **2** | `\| ⟨em⟩⟨em⟩◦ fieldName \| ... \|` |

Required fields are marked with an escaped asterisk `\*` after the (back-ticked)
name, e.g. `` `searchStrategyType`\* ``. The `\` keeps Markdown from reading the
`*` as italics; it renders as a literal `*` meaning "required".

### Example (correct)

```markdown
| Parameters               | Data type | Description                            |
| ------------------------ | --------- | -------------------------------------- |
| `dataFieldDefinitions`\* | Array     | List of field definitions.             |
|   • `fieldId`\*          | String    | The field to search on.                |
|   • `dataSourceDetails`  | Object    | Contains details about the data source.|
|   ◦ `fieldReference`     | String    | Reference to the field in the source.  |
|   • `dataType`           | String    | Data type of the field.                |
```

> The spaces before `•` and `◦` above must be em-spaces (`U+2003`), not the
> spacebar. In an editor they look identical to normal spaces.

---

## Deeply-nested tables (3–4 levels) — mirror ReadMe's markers

Some pages (e.g. `create-promotion-for-ucc`, `get-promotion-details`) nest 3–4
levels deep. For these, **mirror the source ReadMe exactly**: ReadMe uses `•`
for level 1 and repeated hyphens for deeper levels, and it places **every marker
at the same left position** — depth is shown by the *marker*, not by whitespace.

**Indent with normal-width non-breaking spaces (`U+00A0` / nbsp), NOT em-spaces.**
Em-spaces (`U+2003`) are full-width (~1em each), so progressive indenting pushes
the `---`/`----` markers far right and wraps the parameter name onto a second
line — it looks broken. nbsp is ~4× narrower, survives Markdown (ordinary spaces
get stripped from table cells), and gives a clean subtle indent like ReadMe.

| Level | Marker | Indent (nbsp `U+00A0`) |
| --- | --- | --- |
| 1 | `•` | 2 |
| 2 | `--` | 4 |
| 3 | `---` | 6 |
| 4 | `----` | 8 |

```markdown
| `promotion` | Object | Root object. |
|   • `limits` | Object | Promotion limits. |
|     -- `pointsPerCustomer` | Integer | Max points per customer. |
|   • `promotionRestrictions` | Object | Restriction settings. |
|     -- `restrictions` | Object | Various restrictions. |
|       --- `redemptionRestrictions` | Object | Redemption limits. |
|         ---- `name` | Enum | Type of redemption restriction. |
```

> The leading spaces before each marker above are **nbsp** (`U+00A0`), not the
> spacebar and not em-spaces. Single-dash rows like `-isActive` (no space after
> the `-`) are ReadMe artifacts that stay flush-left, matching ReadMe.
>
> Why not CSS? Per-page CSS can't select a table row by its `--`/`•` marker
> text, so the indent must live in the cell content.

### Source of truth for depth

Do not guess the nesting — read the original ReadMe markup:

- **Stored raw files:** `/home/javeed/Documents/CAPILLARY/INFO/backfill/raw/<slug>.md`
  (ReadMe uses HTML `<table>` blocks; the first `<td>` of each row holds the
  literal marker, e.g. `-- pointsPerCustomer`).
- **Or fetch live:** append `.md` to the page URL, e.g.
  `https://docs.capillarytech.com/reference/create-promotion-for-ucc.md`.

---

## How to apply it safely

Because em-spaces are invisible and look exactly like ordinary spaces, **do not
hand-type them.** Instead:

1. **Copy an existing** `•` / `◦` prefix from a correct row and reuse it, **or**
2. Run a small script that replaces the leading ASCII indent with em-spaces.

### Fix script (Python)

Run from the folder containing the target `.mdx` file. Adjust `path`.

```python
import io, re

path = "put-data-field-api.mdx"
EM = " "        # em-space (non-collapsing)
BULLET = "•"    # •
HOLLOW = "◦"    # ◦

with io.open(path, encoding="utf-8") as f:
    lines = f.read().split("\n")

out = []
for ln in lines:
    m = re.match(r"^\|\s+(" + re.escape(HOLLOW) + r"\s.*)$", ln)   # level 2
    if m:
        out.append("| " + EM * 2 + m.group(1)); continue
    m = re.match(r"^\|\s+(" + re.escape(BULLET) + r"\s.*)$", ln)   # level 1
    if m:
        out.append("| " + EM * 2 + m.group(1)); continue
    out.append(ln)

with io.open(path, "w", encoding="utf-8") as f:
    f.write("\n".join(out))
```

### Verify the result

Check that the bytes before the marker are em-spaces (`e28083`), not ASCII
spaces (`20`):

```bash
grep -m1 '• `fieldId`' put-data-field-api.mdx | head -c 20 | xxd
# expect: 7c20 e280 83e2 8083 e280 a2 ...   (| space, 2 em-spaces, •)

grep -m1 '◦ `fieldReference`' put-data-field-api.mdx | head -c 20 | xxd
# expect: 7c20 e280 83e2 8083 e297 a6 ...  (| space, 2 em-spaces, ◦)
```

---

## Action taken (2026-07-15)

- **File:** `reference/put-data-field-api.mdx`
- **Tables fixed:** *Request Parameters* and *Response Body Parameters*
- **Rows updated:** 14 level-1 (`•`) rows + 2 level-2 (`◦`) rows converted from
  ordinary spaces to em-spaces so the indentation renders.
- **fieldReference placement:** set to the "outside" / sibling level — `◦` with
  **2** em-spaces (same indent as the `•` siblings), matching the production
  docs.capillarytech.com rendering rather than being pushed deeper.
- **Also cleaned:** broken backtick artifact
  `` (`\`COMBINATION`, `PREFIX\` ) `` → `` (`COMBINATION`, `PREFIX`) `` and a
  stray space in `` (`TRANSACTION` , `CUSTOMER`) `` → `` (`TRANSACTION`, `CUSTOMER`) ``.
- **Verified:** file compiles cleanly with `@mdx-js/mdx` 3.1.1.

### `create-promotion-for-ucc.mdx` and `get-promotion-details.mdx`

- **Style:** mirror ReadMe markers (`•` / `--` / `---` / `----`) **flush at the
  cell start, no whitespace indentation** — depth shown by the marker only.
- **Rows updated:** 104 (create-promotion-for-ucc, Request Parameters) and 17
  (get-promotion-details, `promotionRestrictions` subtree of Response Parameters).
- **Depth source:** `INFO/backfill/raw/*.md` and the `docs.capillarytech.com/...md`
  raw pages.
- **Content parity checked:** Request 65/65 and Response 52/52 rows present, no
  missing/extra fields; only 15 Request descriptions differ (pre-existing
  `<br/>`/backslash migration artifacts, unrelated to markers).
- **Iterations that were corrected:** (a) markers were briefly swapped to `•`/`◦`
  — restored to ReadMe's `•`/`--`/`---`/`----`; (b) progressive em-space indent
  was added — **removed**, because full-width em-spaces wrapped the deeper rows
  and did not match ReadMe.
- **Verified:** both compile cleanly with `@mdx-js/mdx`.

## Open items

- `reference/create-data-field.mdx` still contains the broken backtick
  `` (`\`COMBINATION`, `PREFIX` ) `` in its *Response Parameters* table
  (left untouched per instruction). Fix if consistency is wanted.
- `reference/create-data-field.mdx` also still indents its `◦ fieldReference`
  rows with **4** em-spaces (pushed deeper). For consistency with the "outside"
  convention above, change those to **2** em-spaces. Left untouched per
  instruction to not edit that file.
- The migrator-output copy
  (`capillary-migrator/output-mdx/docs.capillarytech.com/API Reference/put-data-field-api.mdx`)
  was not updated with the em-space fix; only `docs-capillary-demo` is the
  active edit path.
</content>
