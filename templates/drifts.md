# Drifts — open items

Central list of all open drifts from all component specs. Open this file to know what to do — no need to scan 10 specs.

> **Source-of-truth allocation.** Drifts originate in `<component-folder>/<name>.md` under "Drift notes" — only after the drift test in SKILL.md. This page aggregates.

## Drift test

For every candidate: **"Would MCP code generation from this Figma node produce a visually wrong result?"**
- Yes → drift, listed here.
- No → another bucket. `[VERIFY]` items → `verify-queue.md`. Hardcoded-with-correct-value, dead code, `figma-master-missing` administrative → not here.

## Drift types

| Type | Definition | Fix location |
|---|---|---|
| `value-mismatch` | Code rendered output ≠ Figma | Call site (component code) |
| `token-mismatch` | Token value in `theme/tokens.ts` ≠ Figma | Theme — affects all consumers |
| `component-missing` | Figma element without code component | DEV creates; **agent does not generate** |

## Action enum

The `Action` column tracks decision state per drift. Set by the post-A6 review prompt (see SKILL.md § A6 — Drift review).

| Status | Meaning |
|---|---|
| `OPEN` | Detected, no decision yet. Surfaced by post-A6 prompt on next mapping pass. |
| `ACCEPTED` | User decided code is the truth — mapping-row updated to recognise the code value. Drift closed. |
| `IGNORED` | User decided drift is not worth resolving (cosmetic, below visible threshold). Stays here for record. |
| `SCHEDULED` | Resolution planned for later (e.g., next sprint, design refresh). Surface again on next review. |
| `RESOLVED` | Fix landed (in code or Figma). Drift closed; entry kept for audit-trail. |

---

## For developers (DEV)

### Critical

_(none)_

### Major

| Component | Drift | Location | Action |
|---|---|---|---|
| | | | `OPEN` |

### Minor

| Component | Drift | Location | Action |
|---|---|---|---|
| | | | `OPEN` |

---

## For designers (DESIGNER)

Drifts marked `[DEV+DESIGNER]`: design choice required (change code or update Figma?).

| Component | Drift | What differs | Question for designer | Action |
|---|---|---|---|---|
| | | | | `OPEN` |

---

## Status log

| Date | Component | Drift | Status |
|---|---|---|---|
| | | | |
