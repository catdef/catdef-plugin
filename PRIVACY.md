# Privacy Policy

**Effective:** 2026-05-26
**Last updated:** 2026-05-26

This privacy policy covers the **catdef Claude Code plugin** at `github.com/catdef/catdef-plugin` and the **canonical MCP surface** it connects to at `catdef.org/mcp`.

## What the plugin does locally

The three bundled skills — `/catdef:validate`, `/catdef:scaffold`, `/catdef:extract` — operate on data you supply (JSON artifacts, CSVs, descriptions of catalogs you want to build). **They do not transmit your data anywhere by themselves.** The skills run in your Claude Code session against the vendored spec snapshot in the plugin's `spec/` directory.

## What gets sent to catdef.org/mcp

The plugin's `.mcp.json` registers a connection to `catdef.org/mcp`. When your Claude Code session calls MCP tools on that server, the following requests cross the network:

- **Spec resources** (read-only) — `catdef://spec/CATDEF_SPEC.md`, `catdef://spec/CATIO_SPEC.md`, etc. The server sends spec text; no user data goes to the server.
- **Grounding tools** — `catdef_lookup({term})`, `catdef_list_decisions`, `catdef_describe`. Your query terms reach the server.
- **Validation** — `catdef_validate({artifact})`. The artifact JSON you submit reaches the server for validation; it is not persisted.
- **Feedback** — `catdef_report_feedback({category, severity, body, attribution})`. **Only fired when you (or your AI peer) explicitly file feedback.** The body and any optional attribution you include are persisted in the catdef feedback queue with a CA-NNN identifier.

## What catdef.org/mcp stores

- **Feedback queue** — submitted feedback items with their CA-NNN identifier, body, optional attribution, submission timestamp, current status (received / triaged / decided / shipped / rejected), and a hash of the submitting API key. Retained indefinitely as institutional record of spec governance.
- **API key hashes** — SHA-256 hashes of issued API keys. Plaintext keys are never stored server-side.
- **Standard HTTP request logs** at the hosting layer (Cloudflare). Subject to Cloudflare's own retention policies; see [cloudflare.com/privacypolicy](https://cloudflare.com/privacypolicy).

## Privacy posture

All feedback submissions are **private at submission**. Feedback becomes publicly visible only via explicit curation — when a strategist or maintainer attaches a feedback item to a decision artifact with `make_public: true`, or when the item is cited in a public CA-NNN decision file. This posture is codified in [CA-008](https://github.com/catdef/catdef-spec/blob/main/decisions/CA-008.md).

## What we do not do

- We do not sell, share, or transfer your data to third parties.
- We do not use feedback content for advertising or any marketing purpose without your explicit, separately-obtained consent.
- We do not track you across other websites.
- We do not set cookies. (MCP is JSON-RPC over HTTP with bearer authentication; no browser cookie surface.)

## Your rights

- **Access** — request a copy of feedback items associated with your API key.
- **Correction** — request correction of attribution on submitted feedback.
- **Deletion** — request deletion of feedback items associated with your API key. Granted unless the item has been cited in a public CA-NNN decision (in which case the citation persists but the personal attribution is anonymized).
- **Key revocation** — request revocation of your API key at any time.

Submit any of these requests via email to **scott@catdef.org**.

## Contact

- **Email:** scott@catdef.org
- **Spec repository:** [github.com/catdef/catdef-spec](https://github.com/catdef/catdef-spec)
- **Plugin repository:** [github.com/catdef/catdef-plugin](https://github.com/catdef/catdef-plugin)

## Changes

Material changes to this policy will be announced via a CA-NNN decision artifact in the catdef-spec repository and reflected here with an updated "Last updated" date.

## License

This privacy policy itself is MIT-licensed alongside the plugin and spec.
