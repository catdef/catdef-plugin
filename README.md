# catdef plugin for Claude Code

> catdef is the substrate spec for AI-readable structured data — a JSON shape for describing things (OpenThing) and catalogs of things (OpenCatalog) that any AI runtime can render, validate, or operate on with zero context backfill. Catdef also specifies a serialization standard so that both .OpenThings and .OpenCatalogs can exist on filesystems and be transported via email.
>
> This plugin gives you:
>
> Skills for working with catdef artifacts: `/catdef:validate` lints your JSON; `/catdef:scaffold` generates starter catalogs from a natural-language description; `/catdef:extract` turns existing structured data (CSV, JSON, web pages) into catdef shape.
>
> Canonical MCP surface at [catdef.org/mcp](https://catdef.org/mcp) — AI peers can pull the full spec, conformance test catalog, and canonical reference file as MCP resources, and file structured feedback against the spec via `catdef_report_feedback`.
>
> Reference renderer — live at [render.catdef.org](https://render.catdef.org) (browser-only, L1, no-server) with open source at [github.com/catdef/catdef.org](https://github.com/catdef/catdef.org) for self-hosting or embedding.
>
> Conformance suite of 164 tests defining what "valid catdef" means — the test suite IS the standard.
>
> catdef is part of the OAGP (Open Agentic Governance Pattern) family of specs. The substrate stays small on purpose; any AI-readable structured-data application builds on top.
>
> Spec: [catdef.org](https://catdef.org) · Source: [github.com/catdef/catdef-spec](https://github.com/catdef/catdef-spec) · License: MIT

## Install

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install catdef@claude-community
```

After install, restart your Claude Code session (or run `/reload-plugins`) so the MCP server connects.

## What you get

- **Three skills** auto-invoked when you talk about catdef artifacts:
  - `/catdef:validate` — check a catdef JSON for conformance
  - `/catdef:scaffold` — generate a starter OpenCatalog from a description
  - `/catdef:extract` — turn CSV / JSON / web data into catdef shape
- **Canonical MCP surface** at [catdef.org/mcp](https://catdef.org/mcp) — spec content as resources; feedback channel as tools
- **Vendored spec snapshot** at `spec/` inside the plugin — `CATDEF_SPEC.md`, `CATIO_SPEC.md`, `MCP_REFERENCE.md`, `CONTRIBUTING.md`, plus the canonical reference catalog

## Quick start

### Validate an existing catdef

```
> Is this catdef valid? <paste your .opencatalog JSON>
```

Claude auto-invokes `/catdef:validate` and walks the spec sections, flagging any violations with fix suggestions.

### Scaffold a new catalog

```
> I want to start a catalog of my pottery collection. About 50 pieces — fired
> at different temperatures, glazed in various styles, some with provenance
> notes. Can you scaffold a catdef for me?
```

Claude auto-invokes `/catdef:scaffold`, elicits the catalog identity and key fields, then emits a valid v1.4 OpenCatalog skeleton.

### Extract from a CSV

```
> I exported my Airtable collection to CSV (attached). Turn it into a catdef.
```

Claude auto-invokes `/catdef:extract`, analyzes the columns, proposes a template, and generates the OpenCatalog with all rows as items.

## Skills reference

### `/catdef:validate`

Lints a catdef-shape JSON (OpenCatalog or OpenThing) against catdef v1.4. Reports violations with spec-section references and fix suggestions. When `catdef.org/mcp` is reachable, prefers the server-side mechanical validator (full pytest suite, 164 tests at v1.4); otherwise does rule-based validation from the vendored spec text.

### `/catdef:scaffold`

Generates a starter v1.4 OpenCatalog from a natural-language description. Elicits catalog identity, item template shape, and 4–8 key fields; produces a complete, validatable skeleton. Uses the canonical reference at `spec/canonical/riverside-heritage-reference-v1.4.opencatalog` as the shape reference.

### `/catdef:extract`

Three-phase extraction: (1) analyze source data, (2) propose a template with field-type mappings, (3) emit the OpenCatalog after user approval. Supports CSV, JSON, HTML tables, and web pages.

## MCP surface

The plugin's `.mcp.json` points at the canonical MCP surface at [catdef.org/mcp](https://catdef.org/mcp), specified in [proposals/catdef-org-mcp-canonical-surface.md](https://github.com/catdef/catdef-spec/blob/main/proposals/catdef-org-mcp-canonical-surface.md). The surface exposes:

- **Spec resources** (anonymous tier) — `catdef://spec/CATDEF_SPEC.md`, `catdef://spec/CATIO_SPEC.md`, `catdef://spec/MCP_REFERENCE.md`, etc.
- **Grounding tools** (anonymous tier) — `catdef_lookup`, `catdef_list_decisions`, `catdef_validate`
- **Feedback tools** (standard tier, self-serve api-key) — `catdef_report_feedback`, `catdef_get_feedback_status`
- **Triage tools** (elevated tier, Director-issued key) — `catdef_list_feedback`, `catdef_set_feedback_status`, `catdef_attach_decision`

See the full [MCP_REFERENCE.md §15](https://github.com/catdef/catdef-spec/blob/main/MCP_REFERENCE.md) for the complete surface description.

When the MCP server is unreachable the skills continue to function — Claude Code's MCP reconnection logic handles graceful degradation, and skill-based validation reads the vendored spec snapshot directly.

## Versioning

Plugin version tracks the bundled spec version. Currently v1.4.0 (vendors catdef-spec v1.4).

- **Patch** (1.4.x) — plugin-only fixes, skill wording improvements, README clarifications
- **Minor** (1.x.0) — when catdef-spec ships a new minor (v1.5 etc.), the plugin re-vendors and bumps to match
- **Major** (x.0.0) — only if the plugin's own architecture breaks (e.g., the skills surface changes incompatibly)

## Feedback

Once `catdef.org/mcp` is operational, file structured feedback via the `catdef_report_feedback` tool — it lands in the canonical CA-NNN queue per [CA-009](https://github.com/catdef/catdef-spec/blob/main/decisions/CA-009.md). Until then, open issues at [github.com/catdef/catdef-spec/issues](https://github.com/catdef/catdef-spec/issues).

## License

MIT. See [LICENSE](LICENSE).

## Spec authority

The plugin vendors a snapshot of the spec at install time. The canonical spec lives at [github.com/catdef/catdef-spec](https://github.com/catdef/catdef-spec) and at [catdef.org](https://catdef.org). When the vendored snapshot and the canonical spec disagree, **the canonical spec wins** — re-install the plugin or wait for the next plugin version to pick up updates.

The plugin is maintained by the catdef maintainers (`catdef-maintainer@catdef.org`) under the OAGP-family governance pattern documented in [github.com/catdef/catdef-spec/blob/main/CLAUDE.md](https://github.com/catdef/catdef-spec/blob/main/CLAUDE.md).
