# Verify queue — open `[VERIFY]` items

Items that need to be verified in a **next session with active Figma design file (live MCP)**. Not drift; not tech debt; only unconfirmed assumptions that make the mapping correct once they have been checked.

> **Source-of-truth allocation.** `[VERIFY]` items originate in component specs or cache files when mapping is derived from indirect evidence (instance-id format, code-search result, convention). This page also accepts entries written by the sister `figma-to-code-implement` skill — see "Entry sources" below. This page aggregates.

## Entry sources

| Source | Entry type | Example |
|---|---|---|
| Mapping-skill (this skill) — A4 mapping pass | Unconfirmed token-name, derived master-id, unverified style override | `Token blue/12 — figma-name unconfirmed; mapping inferred from context` |
| Implement-skill (sister) — fingerprint hygiene-gap | `frame-should-be-instance` — implement found an element-frame matching an existing code-component by fingerprint, user confirmed the match | `Frame "Submit CTA" (node 1234:5678) should be instance of Button master per primary-50+radius-md+text-button fingerprint; user-confirmed at <date>` |

On the next mapping pass, the mapping agent reads these entries and resolves them: tokens get promoted into `tokens.md` once confirmed, frame-should-be-instance entries get promoted into `components.md` with `figma-master-missing` note plus a `drifts.md` entry for the Figma-hygiene gap.

## Drift-test boundary

A `[VERIFY]` item becomes a drift the moment it has been confirmed via MCP/code check that the rendered output ≠ Figma. Until then: here, not in `drifts.md`.

---

## Open

| Topic | What to verify | Where | How to resolve |
|---|---|---|---|
| | | | |

---

## Resolved

| Date | Topic | How resolved |
|---|---|---|
| | | |

## Status log

| Date | Item | Status |
|---|---|---|
| | | |
