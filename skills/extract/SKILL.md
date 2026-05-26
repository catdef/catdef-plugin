---
name: extract
description: Extract fields from existing structured data (CSV, JSON, web pages, spreadsheets) into catdef-shape OpenCatalog. Use when the user asks to "turn this CSV into a catdef", "extract fields from this data into a catalog", "convert this JSON to OpenCatalog", or asks to migrate an existing dataset into catdef shape. Analyzes the source, proposes a template, maps source fields to canonical catdef field types, and emits a valid v1.4 OpenCatalog with the extracted items.
---

# /catdef:extract

Extract catdef-shape catalog data from an existing source (CSV, JSON, HTML table, spreadsheet, web page describing a collection).

## When to invoke

Invoke when the user:

- Asks to "turn this CSV into a catdef" / "extract fields from this data into a catalog" / "convert this JSON to OpenCatalog"
- Pastes structured data (CSV, JSON) and asks for it as catdef
- Points at a URL describing a collection and asks for it as catdef
- Asks to migrate an existing dataset (Airtable export, Google Sheet, Notion database) into catdef

## Three-phase procedure

### Phase 1 — Analyze the source

Read enough of the source to identify:

- **The unit being cataloged** — one row = one item; one JSON object = one item; one HTML table row = one item
- **Field shape** — what columns/keys are present; what types they look like (text vs number vs date vs enumerated)
- **Cardinality of each field** — required? Unique? Multi-value?
- **Any obvious subcat candidates** — fields where the value is drawn from a small recurring vocabulary (e.g., `Brand`, `Category`, `Country`)
- **Photo fields** — URLs or file paths that look like images
- **Data quality issues** — missing values, inconsistent formats, mixed types

Report back what you see before generating. Don't silently make decisions for the user.

### Phase 2 — Propose a template

Map source fields to catdef field types from the canonical vocabulary in CATDEF_SPEC.md:

| Source pattern | catdef type |
|---|---|
| Free-text string | `Text` |
| Numeric (integer or decimal) | `Number` |
| Closed list with ≤20 distinct values | `Enumerated` (consider subcat) |
| Closed list with >20 distinct values | `Enumerated` without subcat (or `Text` if cardinality is huge) |
| ISO date or recognizable date string | `Date` |
| Money: amount + currency code | `Currency` |
| Image URL or file path | `Photo` |
| Nested structured data per row | `Table` |

Propose the template to the user before generating items. Offer to:

- Rename source columns to user-friendlier field names
- Drop fields that look noisy or duplicative
- Mark fields as `required: true` based on actual cardinality in the source

### Phase 3 — Emit the OpenCatalog

After the user approves the template, generate the full v1.4 OpenCatalog:

```json
{
  "catdef": "1.4",
  "product": { ... },
  "templates": [ { proposed-template } ],
  "subcats": { ... if any proposed },
  "items": [
    { /* one per source row */ }
  ]
}
```

Validate the result with `/catdef:validate` before reporting done.

## Source-specific tips

### CSV

- The header row becomes the field names
- Watch for type inference: a column of `1, 2, 3, ...` is `Number`; a column of `1, 2, 3, three` is `Text`
- Quote/escape handling matters — read with a real CSV library if available, not regex

### JSON

- If the top-level shape is an array of objects, each object is an item; the union of keys (most-common subset) is the template
- If the top-level is a single object with nested arrays, ask the user which array represents items

### Web pages

- Use the `WebFetch` tool if available; otherwise ask the user to paste the relevant HTML or describe the table
- Look for `<table>` elements with `<thead>`/`<tbody>` structure
- Lists of items with consistent CSS classes often map to items
- If the page is a single-item description, the result is an **OpenThing**, not an OpenCatalog

### Spreadsheets (Google Sheets, Airtable, Excel)

- Usually have a header row + data rows pattern — treat as CSV after export
- Multi-tab spreadsheets: ask the user which tab; one tab per template if the user wants multiple

## What to ask before extracting

Don't extract silently. Confirm with the user:

1. **Catalog identity** — name, slug, description (the extraction generates the items; the user must own the catalog metadata)
2. **Which fields to keep** — if the source has 30 columns, propose a starter subset (4–8) and offer the rest for a "include all" pass
3. **How to handle missing data** — empty strings vs null vs drop the field
4. **Photo handling** — if photo columns exist, are URLs OK or should photos be downloaded and bundled into a CATIO bundle?

## What NOT to do

- **Don't invent field types** — only the canonical vocabulary; if a source field doesn't fit cleanly, use `Text` and surface the question
- **Don't auto-generate `x.` extension fields** for source columns you can't classify — surface them instead
- **Don't fabricate IDs, slugs, or canonical values** that don't exist in the source — ask
- **Don't drop policy-bearing source fields silently** — if a source has something that looks like a license, a privacy flag, or a usage restriction, surface it before deciding how to represent it (an `x.` extension field or a future policy declaration)

## Reference

- Spec: `${CLAUDE_PLUGIN_ROOT}/spec/CATDEF_SPEC.md` (§field types is the load-bearing reference for the mapping table)
- Canonical reference: `${CLAUDE_PLUGIN_ROOT}/spec/canonical/riverside-heritage-reference-v1.4.opencatalog` — shows what a fully-formed catalog looks like, useful as a target shape
- CATIO bundle format (for photo-bundled extraction): `${CLAUDE_PLUGIN_ROOT}/spec/CATIO_SPEC.md`
