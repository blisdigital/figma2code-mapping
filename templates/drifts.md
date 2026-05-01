# Drifts — open punten

Centrale lijst van alle open drifts uit alle component-specs. Een ontwikkelaar opent
dit bestand om te weten wat er te doen is — niet 10 specs scannen.

> **Bron-relatie.** Drifts ontstaan in `components/<naam>.md` onder de sectie
> "Drift-aandachtspunten". Deze pagina aggregeert; bij elke pas waarin drifts worden
> opgelost: hier én in de spec markeren als `[opgelost]` of weghalen.
>
> **Audience-filtering.** Per audience een sectie. Bij `[DEV+DESIGNER]` staat een drift
> in beide secties.

## Drift-typen — quick reference

| Type | Severity-default | Beschrijving |
|---|---|---|
| `token-mismatch` | per dimensie (zie SKILL.md) | Code-token-waarde ≠ Figma-waarde |
| `value-mismatch` | per dimensie | Hardcoded code-waarde ≠ Figma-waarde |
| `token-not-applied` | altijd Minor | Hardcoded matcht token — token niet gebruikt |
| `component-missing` | altijd Major | Figma-element zonder code-component (DEV maakt; agent niet genereren) |

---

## Voor ontwikkelaars (DEV)

Sorteer op severity: Critical → Major → Minor.

### Critical

| Component | Drift | Locatie | Actie |
|---|---|---|---|
| | | | |

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

Bij `[DEV+DESIGNER]`-drifts: design-keuze nodig (code aanpassen of Figma updaten?).

| Component | Drift | Wat verschilt | Vraag aan designer |
|---|---|---|---|
| | | | |

---

## Status-log

| Datum | Component | Drift | Status (open / opgelost / niet-doen) |
|---|---|---|---|
| | | | |
