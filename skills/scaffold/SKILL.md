---
name: scaffold
description: Generate a starter catdef-shape OpenCatalog from a natural-language description of what the user wants to catalog. Use when the user asks to "create a catdef for X", "scaffold a catalog for Y", "I want to make a catalog of Z", or describes a collection of things they want to track. Produces a complete, validatable v1.4 OpenCatalog JSON skeleton with templates, field definitions, and a minimal-required product block.
---

# /catdef:scaffold

Generate a starter OpenCatalog from a natural-language description.

## When to invoke

Invoke when the user:

- Asks to "create a catdef for X" / "scaffold a catalog for Y" / "I want to make a catalog of Z"
- Describes a collection of things they want to track ("I collect watches", "I run a small museum", "I track wine I've tasted")
- Asks "how would I model X in catdef?"
- Pastes a brief catalog idea and asks for a starting skeleton

## What to elicit before scaffolding

Before generating, get answers to:

1. **Catalog identity** — name, slug (short URL-safe identifier), tagline (one sentence), description (one paragraph)
2. **Item template shape** — what is the primary thing being cataloged? (e.g., Watch, Wine, Artifact)
3. **Key fields** — what would a user want to record about each item? Aim for 4–8 fields for a starter; the user can extend later
4. **Field types** — for each key field, pick from the canonical catdef field type vocabulary:
   - `Text` (free text)
   - `Number` (numeric)
   - `Enumerated` (closed list of values, optionally enriched via subcats)
   - `Currency` (amount + ISO 4217 code)
   - `Date` (ISO 8601)
   - `Photo` (single image, with optional transform)
   - `Table` (structured rows — use sparingly in a starter)
5. **Subcats** (optional) — if any Enumerated field has natural enrichment (e.g., Brand → has founding-year + country), offer to scaffold a subcat block
6. **Theme** (optional) — light or dark default; the spec has a theme block

If the user is impatient or asks for "just give me something to start with", elicit minimally (just catalog name + item type + 3 fields) and produce a deliberately-small starter.

## Scaffold output shape

Produce a complete, valid v1.4 OpenCatalog. Use the canonical reference at `${CLAUDE_PLUGIN_ROOT}/spec/canonical/riverside-heritage-reference-v1.4.opencatalog` as the shape reference. Minimum structure:

```json
{
  "catdef": "1.4",
  "product": {
    "name": "<catalog name>",
    "slug": "<url-safe-slug>",
    "tagline": "<one sentence>",
    "description": "<one paragraph, may use simple HTML>",
    "owner": "<user's name or organization>"
  },
  "templates": [
    {
      "name": "<ItemType>",
      "field_defs": [
        { "name": "<Field1>", "type": "<Type>", "required": true },
        { "name": "<Field2>", "type": "<Type>" }
      ]
    }
  ]
}
```

Add `subcats`, `views`, `themes`, `embed`, and `settings` blocks only if the user wants them or if the catalog clearly needs them. Default to **minimal** — the catdef philosophy is one-file-complete-product, but starter catalogs should be small enough to read in 30 seconds.

## After scaffolding

- Suggest the user save the result as `<slug>.opencatalog` (raw JSON; CATIO bundle only needed when photos attach)
- Offer to validate the scaffold (`/catdef:validate`) before they ship it
- Suggest next steps: add a few example items, attach a photo, define a subcat for an Enumerated field

## What NOT to scaffold

- **Don't invent field types** — only the canonical vocabulary in CATDEF_SPEC.md
- **Don't add `x.` extension fields without asking** — the extension namespace is for real downstream-tool needs, not speculative future capabilities
- **Don't generate policy declarations on the user's behalf** — policies (e.g., `.machine-translate: "Never"`) are intent declarations that the user must own; ask before adding any
- **Don't auto-fill optional fields with placeholders** — empty is better than `"TODO"` or `"<your name here>"` strings, which become silent bugs

## Reference

- Spec: `${CLAUDE_PLUGIN_ROOT}/spec/CATDEF_SPEC.md` (substrate; read §Top-Level Structure, §templates, §field types, §subcats)
- Canonical reference (a known-valid, fully-fleshed-out catalog to copy shape from): `${CLAUDE_PLUGIN_ROOT}/spec/canonical/riverside-heritage-reference-v1.4.opencatalog`
- Field type closed vocabulary: CATDEF_SPEC.md §field types
