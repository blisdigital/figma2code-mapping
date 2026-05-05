# Drifts — open punten

Centrale lijst van alle open drifts uit alle component-specs. Open dit bestand om te weten wat te doen — geen 10 specs scannen.

> **Bron-relatie.** Drifts ontstaan in `components/<naam>.md` onder "Drift-aandachtspunten" — pas na de drift-test in SKILL.md. Deze pagina aggregeert.

## Drift-test

Voor elke kandidaat: **"Zou MCP-codegen vanuit deze Figma-node een visueel verkeerd resultaat opleveren?"**
- Ja → drift, hier vermeld.
- Nee → andere bucket. `[VERIFY]`-items → `verify-queue.md`. Hardcoded-met-juiste-waarde, dead code, `figma-master-missing` administratief → niet hier.

## Drift-typen

| Type | Definitie | Fix-locatie |
|---|---|---|
| `value-mismatch` | Code rendered output ≠ Figma | Call-site (component code) |
| `token-mismatch` | Token-waarde in `theme/tokens.ts` ≠ Figma | Theme — affects all consumers |
| `component-missing` | Figma-element zonder code-component | DEV maakt; **agent niet genereren** |

---

## Voor ontwikkelaars (DEV)

### Critical

_(geen)_

### Major

| Component | Drift | Locatie | Actie |
|---|---|---|---|
| | | | |

### Minor

| Component | Drift | Locatie | Actie |
|---|---|---|---|
| | | | |

---

## Voor designers (DESIGNER)

Drifts met `[DEV+DESIGNER]`: design-keuze nodig (code aanpassen of Figma updaten?).

| Component | Drift | Wat verschilt | Vraag aan designer |
|---|---|---|---|
| | | | |

---

## Status-log

| Datum | Component | Drift | Status |
|---|---|---|---|
| | | | |
