# Verify queue — open `[VERIFY]` items

Items that need to be verified in a **next session with active Figma design file (live MCP)**. Not drift; not tech debt; only unconfirmed assumptions that make the mapping correct once they have been checked.

> **Source-of-truth allocation.** `[VERIFY]` items originate in component specs or cache files when mapping is derived from indirect evidence (instance-id format, code-search result, convention). This page aggregates.

## Entry sources

| Source | Entry type | Example |
|---|---|---|
| Mapping-skill — A4 mapping pass | Unconfirmed token-name, derived master-id, unverified style override | `Token blue/12 — figma-name unconfirmed; mapping inferred from context` |
| Mapping-skill — A4-classify, user refused promotion | `frame-considered-not-promoted` — record so the next pass doesn't re-ask | `Frame "card-shell" — incidental alignment with Card, user opted-out` |

On the next mapping pass the mapping agent reads these entries and resolves them: tokens promoted into `tokens.md` once confirmed, user-refused promotions stay recorded so they aren't re-asked.

## Drift-test boundary

A `[VERIFY]` item becomes a drift the moment it has been confirmed via MCP/code check that the rendered output ≠ Figma. Until then: here, not in `drifts.md`.

**Exception — `frame-considered-not-promoted` entries** are not drift candidates. They are cross-session decision-memos so the next mapping pass does not re-propose the same promotion. They never become drifts; they remain recorded until the underlying frame disappears from Figma.

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
