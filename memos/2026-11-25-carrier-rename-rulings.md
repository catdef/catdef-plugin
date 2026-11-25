# MEMO — Carrier rename: §4 rulings

- **To:** catdef implementer seat
- **From:** Muse, at the direction of Scott Welch
- **Date:** 2026-11-25
- **Status:** Ruled — the scope questions in the 2026-11-16 carrier-rename memo are closed
- **Subject:** CATX (CATalog Xchange) — the five §4 rulings, and what executes next

---

## 1. Background

The 2026-11-16 memo recorded the decision to rename the catdef carrier from
"openthing" to **CATX** and left five scope questions (§4) open for the
strategist seat. On 2026-11-25 the strategist seat ruled on all five. The
rulings are below; they are final unless a later memo supersedes them.

The driver, for the record: airtrōv will make heavy use of CATX, and its
export generator stamp could not change until these rulings landed.

## 2. The rulings

1. **What CATX names — the whole carrier family.** CATX is the name of the
   carrier in all its forms: file, bundle, envelope, and wire. "catio"
   retires as a name; CATIO_SPEC becomes the CATX envelope spec.
2. **Extensions — `.catx` replaces all three.** New files use `.catx`
   whether they carry a catalog, a single thing, or a schema; the document
   type is stated inside the file, in the envelope `type` field, not in the
   extension. `.opencatalog`, `.openthing`, and `.catdef` are retired as
   extensions for new files.
3. **MIME types — CATX successors.** `application/vnd.catx+json` (document)
   and `application/vnd.catx+zip` (bundle) succeed
   `application/vnd.catdef+json` and `application/vnd.opencatalog+zip`.
   Emitters switch at spec 1.5; the old types are accepted on read
   indefinitely (see §3).
4. **Envelope keys — the version field becomes `catx`.** Writers emit the
   `catx` key; readers accept both `catio` and `catx` keys indefinitely.
   Document `type` values — `catalog` / `thing` / `schema` — are unchanged.
   The spec stamp advances to 1.5 with the rev (§2.5).
5. **Versioning — a minor rev: spec 1.5.** The rename rides CATDEF_SPEC
   1.5 (and the envelope spec with it). The data model does not change —
   templates, field types, and data shapes are exactly as in 1.4 — so this
   is not a 2.0. The rev exists because the wire-visible changes (extension,
   MIME types, envelope key, dual-accept reader requirements) are normative
   content the 1.4 text does not carry.

## 3. Compatibility invariants (confirmed, not just proposed)

- **Content-sniff, never trust the extension** — already spec doctrine; the
  transition depends on it.
- Every existing `.opencatalog` / `.openthing` file in the wild keeps
  reading, indefinitely. Files in the wild keep their names; nothing is
  renamed retroactively.
- Legacy MIME types and the legacy `catio` envelope key are accepted on
  read indefinitely. There is no sunset date for reading the old forms.
- The conformance suite covers legacy and CATX forms side by side for the
  whole transition.

## 4. What executes next (implementer seat)

- Spec text rev to 1.5: CATDEF_SPEC and the envelope spec (re-badged as the
  CATX envelope spec), stating the new extension, MIME types, and envelope
  key, and the dual-accept reader requirements.
- Conformance suite: add CATX-form cases alongside the legacy ones.
- Reference renderer: accept `.catx` and the CATX MIME types (legacy
  accept-list retained).
- airtrōv export generator: stamp changes to the CATX forms once 1.5 is
  published.
- microtrov, building against `.opencatalog`, adopts CATX on these rulings.

## 5. Announcement

Per the strategist seat's trumpet ruling (minimum): an announcement on
catdef.org and this memo on GitHub. Fuller announcement mechanics, if any,
are a later ruling.
