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

---

## For developers (DEV)

### Critical

_(none)_

### Major

| Component | Drift | Location | Action |
|---|---|---|---|
| | | | |

### Minor

| Component | Drift | Location | Action |
|---|---|---|---|
| | | | |

---

## For designers (DESIGNER)

Drifts marked `[DEV+DESIGNER]`: design choice required (change code or update Figma?).

| Component | Drift | What differs | Question for designer |
|---|---|---|---|
| | | | |

---

## Status log

| Date | Component | Drift | Status |
|---|---|---|---|
| | | | |
