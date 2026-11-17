# MEMO — Proposed carrier rename: openthing → CATX

- **To:** catdef strategist seat; catdef implementer seat (both to be seated by Scott Welch)
- **From:** Muse, at the direction of Scott Welch
- **Date:** 2026-11-16
- **Status:** Proposed — decision recorded, scope questions open for the strategist seat
- **Subject:** Rename the catdef carrier from "openthing" to **CATX** (CATalog Xchange), extension `.catx`

---

## 1. The decision on record

In the airtrōv AI-first session of 2026-11-15, Scott Welch renamed the carrier:
**openthing → CATX**, short for **CATalog Xchange** (the X is eXchange), file
extension `.catx`. It is item 1 on the airtrōv launch list from that session:
"rev catdef to CATX and trumpet the carrier."

This memo details the proposed change so the seated strategist and implementer
can rule on scope and execute. It proposes **no change to the data model** —
templates, field types, and data shapes are untouched. This is a carrier and
naming revision, unless the strategist seat rules otherwise.

## 2. Current state (verified 2026-11-16)

- Document family: `.opencatalog` (catalog with data — raw JSON or a ZIP
  bundle; bundle MIME `application/vnd.opencatalog+zip`), `.openthing`
  (single object, document type `"thing"`), `.catdef` (schema only).
  Document MIME: `application/vnd.catdef+json`.
- Transport envelope is named **catio** (CATIO_SPEC v1.4). Live exports stamp
  `catio: "1.0"`, `catdef: "1.4"`.
- Specification is CATDEF_SPEC v1.4; the canonical bundle targets
  "catdef 1.4-draft" and v1.3 is the current *released* spec. Readers are
  already required to be version-tolerant.
- Conformance is mechanical, not prose: the pytest suite in
  `catdef/catdef-spec` is the standard ("the test suite IS the standard").
- The reference renderer (render.catdef.org) accepts
  `.openthing .opencatalog .catdef`.
- Real files in the wild — airtrōv exports, the canonical Riverside Heritage
  bundle — use `.opencatalog`. **No file anywhere uses `.catx` yet.** CATX is
  currently a decision, not a format; this memo starts the work of making it one.

## 3. Rationale

- The carrier's job is **exchange**: airtrōv exports a Collection and an agent
  imports it elsewhere; the export doubles as the import audit; microtrov and
  per-collection Workers are fed by the carrier file alone. "Openthing" names
  a *thing* — and the lesser document type at that — not the exchange of
  catalogs, which is the primary payload and the entire point.
- CATX names the function in one word an arriving agent can be told:
  collections are extractible to **CATX**, an open standard, before signup.
  The airtrōv agent card (`/.well-known/agent-card.json`) and the public floor
  both need that single word.
- A coined, rare name is attributable: uptake of "CATX" is measurable signal,
  not noise — the same tracer logic as the rest of the memetic programme.

## 4. Scope questions for the strategist seat

The rename is decided; its exact reach is not. The strategist rules on:

1. **What CATX names.** The whole carrier family (replacing "catio" as the
   transport name), or the file-level format only, with catio surviving as
   the envelope spec's name?
2. **Extensions.** Does `.catx` replace `.opencatalog` for catalogs? What
   happens to `.openthing` and `.catdef` — retired, retained as document
   types within CATX, or renamed with it?
3. **MIME types.** Do `application/vnd.catdef+json` and
   `application/vnd.opencatalog+zip` gain CATX successors, and on what
   timeline do the old ones sunset?
4. **Envelope keys.** Does the `catio` version field become `catx`? Document
   `type` values (`catalog` / `thing` / `schema`) are presumed unchanged —
   confirm.
5. **Versioning.** Does the rename ride a spec rev (1.5? 2.0?), or is it a
   naming layer over 1.4 semantics with the spec text re-badged?

## 5. Compatibility invariants (proposed, for implementer guidance)

- **Content-sniff, never trust the extension** — already spec doctrine; the
  transition depends on it.
- Every existing `.opencatalog` / `.openthing` file in the wild must keep
  reading, indefinitely. The installed base is small but real, and the
  format's promise is permanence: a CATX plus microtrov runs forever.
- The conformance suite must cover legacy and CATX forms side by side for
  the whole transition; a file that passed before the rename passes after it.
- airtrōv's export generator stamp (`generator` block, `catio` / `catdef`
  version fields) changes only when the strategist's §4 rulings land.

## 6. Who is counting on this

- **microtrov** — the open-source in-memory single-catalog server/viewer, and
  its edge form (a per-collection Cloudflare Worker fed only by the carrier
  file, kept fresh by an edition stamp). Being built now against
  `.opencatalog`; adopts CATX when the rulings land.
- **The airtrōv AI-first front door** — agent card, llms.txt, and the public
  floor all name the carrier when telling an arriving agent that Collections
  are portable.
- **airtrōv launch** — the CATX rev is launch item 1; the front door and the
  demo arc ("export my collection and build me a printed PDF catalog") both
  speak the carrier's name.

## 7. The asks

- **Strategist seat:** rule on §4 (1–5), and on the "trumpet" plan — how
  catdef.org, the spec, and the renderer announce the rename.
- **Implementer seat:** on those rulings — spec text rev, conformance suite
  updates, renderer accept-list, and the airtrōv generator stamp.

Scott is seating both roles. Replies and rulings belong in this repo as
further memos, so the decision history stays git-blameable.

— Muse, 2026-11-16
