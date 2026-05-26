---
name: validate
description: Lint a catdef-shape JSON artifact (OpenCatalog or OpenThing) against the catdef v1.4 specification. Use when the user asks to "check this catdef", "validate my OpenCatalog file", "is this catdef valid?", or pastes a JSON artifact and asks if it conforms to catdef. Reports conformance violations with spec-section references and suggests fixes.
---

# /catdef:validate

Validate a catdef artifact (OpenCatalog or OpenThing) against the catdef v1.4 specification.

## When to invoke

Invoke when the user:

- Asks to "check this catdef" / "validate my OpenCatalog file" / "is this catdef valid?"
- Pastes a JSON artifact and asks whether it conforms to catdef
- Asks for help interpreting a catdef validation error
- Asks whether a proposed structural change to a catalog stays within spec

## What you need before validating

- The artifact (raw JSON, a `.opencatalog` or `.openthing` file, or a ZIP-packaged CATIO bundle)
- Knowledge of which catdef version the artifact targets (read the top-level `"catdef"` field — required)
- Access to the vendored spec at `${CLAUDE_PLUGIN_ROOT}/spec/CATDEF_SPEC.md` (substrate) and `${CLAUDE_PLUGIN_ROOT}/spec/CATIO_SPEC.md` (bundled transport)

## Validation procedure

Walk these checks in order. Report the first failure with the spec section that's violated; continue to gather all failures rather than stopping at the first.

### 1. Top-level shape (CATDEF_SPEC.md §Top-Level Structure)

- `"catdef"` field present and a valid semver string
- One of the role keys present: `"product"` (OpenCatalog) or top-level item shape (OpenThing)
- Required fields per the artifact's role (read the spec for the exact list)
- No unknown top-level keys without the `x.` extension namespace prefix (CATDEF_SPEC.md §extension-namespace)

### 2. Templates (if present) — CATDEF_SPEC.md §templates

- Each template has a `name` (unique within the catalog)
- Each `field_defs` entry has `name`, `type`, and valid type-specific attributes
- Field types are drawn from the canonical type vocabulary (see spec for the closed list)
- No `field_def` shadows a reserved name

### 3. Field values (if present) — CATDEF_SPEC.md §field types

For each item field, check that the value matches the field type:

- `Text` → string
- `Number` → numeric
- `Enumerated` → value present in the field's `values` array OR a subcat-attached value
- `Currency` → object with `amount` (number) and `currency` (ISO 4217 code)
- `Date` → ISO 8601 date string
- `Photo` → photo descriptor (URL or attached photo ID) with valid `transform` if present (rotation MUST be 0/90/180/270)
- `Table` → array of row objects matching the table's `columns` definition
- Other field types — consult the spec section for the type

### 4. Subcats (if present) — CATDEF_SPEC.md §subcats

- Each subcat-enabled field has a defined `subcat` block
- All values referenced in items exist in the subcat's `values` array
- Enrichment fields validate against their own type definitions

### 5. Policies (if present) — CATDEF_SPEC.md §policies; value #9

The policy vocabulary is **closed**. A renderer or tool that silently ignores a declared policy is non-conformant. Check:

- Policy keys are drawn from the canonical policy vocabulary (e.g., `.machine-translate`)
- Policy values are drawn from the canonical value vocabulary for that key (e.g., `"Never"`)
- Custom or extension policies use the `x.<domain>.policy.<name>` extension shape

### 6. CATIO bundle (if applicable) — CATIO_SPEC.md

If the artifact is a ZIP-packaged `.opencatalog` or `.openthing`:

- Root contains the JSON document with the matching extension
- Outer archive extension matches the inner JSON role (`.opencatalog` outer for `.opencatalog` JSON)
- Photo references resolve to bundled photo files (or external URLs)
- See CATIO_SPEC.md §Bundled Transport for the full rules

### 7. Forward-compat sanity — value #5

- Required fields aren't missing
- Optional fields with unknown names get a warning (extension-namespace candidate?), not an error
- Version-stamping: the `"catdef"` value reflects the lowest spec version that defines every feature used in the artifact

## Reporting

Format violations as:

```
ERROR: <human-readable description>
  Spec: <SPEC_FILE.md §section-name>
  Path: <JSON path to the offending value, e.g., items[3].fields.Currency>
  Found: <actual value or shape>
  Expected: <what the spec requires>
  Fix: <one-line suggestion>
```

For passing artifacts, report:

```
✓ Valid catdef v<version> <role>
  Templates: N | Items: N | Subcats: N | Policies: N
  Forward-compat stamp: catdef <version>
```

## When the canonical MCP surface is reachable

If `catdef.org/mcp` is connected (check via `/mcp` or by trying to call `catdef_validate`), prefer the server-side mechanical validation over the rule-based pass — the server runs the full pytest conformance suite (164 tests at v1.4) and returns a structured result. Use the rule-based skill as fallback when the MCP server is unreachable, or for educational explanations of spec violations.

## Reference

- Spec: `${CLAUDE_PLUGIN_ROOT}/spec/CATDEF_SPEC.md`
- CATIO transport: `${CLAUDE_PLUGIN_ROOT}/spec/CATIO_SPEC.md`
- Canonical reference (a known-valid catalog to compare shapes against): `${CLAUDE_PLUGIN_ROOT}/spec/canonical/riverside-heritage-reference-v1.4.opencatalog`
- Full pytest conformance suite: [github.com/catdef/catdef-spec/tree/main/conformance](https://github.com/catdef/catdef-spec/tree/main/conformance)
