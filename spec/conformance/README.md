# Conformance test suite — pointer

The catdef conformance suite is a pytest harness, not vendored in this plugin.

Full suite: [github.com/catdef/catdef-spec/tree/main/conformance](https://github.com/catdef/catdef-spec/tree/main/conformance)

As of catdef v1.4: 164 tests covering CATIO bundle parsing, field values, i18n, photo transforms, subcat values, and versioning. The test suite IS the standard — anyone can build a renderer; to call it conformant, it passes the suite.

This plugin's `/catdef:validate` skill performs **rule-based validation** by having Claude read the spec text (vendored alongside this file at `../CATDEF_SPEC.md`) and check an artifact against it. For mechanical conformance verification, run the pytest suite against your runtime directly.

When `catdef.org/mcp` is operational (per [CA-008 Directive 3](https://github.com/catdef/catdef-spec/blob/main/decisions/CA-008.md)), the `catdef_validate` MCP tool will offer server-side mechanical validation as a third option.
