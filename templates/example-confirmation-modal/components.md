# Components — index — Voorbeeld (Blis)

> **Dit is een ingevuld voorbeeld** ter referentie. Kopieer niet rechtstreeks — gebruik
> `templates/components.md` voor je eigen project.

## Atomic design — drie niveaus

**Atoms** zijn onsplitsbaar. **Molecules** zijn samenstellingen met één doel.
**Organisms** zijn complexe samenstellingen met state, scroll-gedrag, of
keyboard-handling.

## Selectie-volgorde — organisms eerst

Bij het mappen van een Figma-frame naar code: **check eerst of een organism past, dan
molecules, dan atoms.**

---

## Atoms

| Component | Uses | Beschrijving | Detail | Figma |
|---|---|---|---|---|
| `Button` | — | Primaire knop op MUI-base, sizes small/medium en color variants (incl. `highlight`). Lift-hover en active-press, focus-visible 2px white outline | `[TODO]` | `[TODO]` |
| `SecondaryButton` | — | Outlined cancel/secondary knop, white background met blue[30] border | `[TODO]` | `[TODO]` |
| `IconButton` | — | Icon-only knop, kan rounded zijn (pill) of niet (md radius) | `[TODO]` | `[TODO]` |

> **Note.** Alle drie de buttons leven in `src/components/ui/button/` met gedeelde
> styles. Niet als aparte specs uitgewerkt in deze pas — alleen geregistreerd voor de
> ConfirmationModal-mapping.

## Molecules

*Geen molecules gedocumenteerd in deze pas.*

## Organisms

| Component | Uses | Beschrijving | Detail | Figma |
|---|---|---|---|---|
| `Modal` | — | Generieke modal-wrapper rondom `react-modal` met header (title + close), body, en optionele footer-actions. Desktop: 604px gecentreerd; mobile: full-screen | `[TODO]` | `[TODO]` |
| `ConfirmationModal` | `Modal`, `Button`, `SecondaryButton` | Bevestigings-dialoog met titel, message, annuleren + bevestigen-knop, en loading-state | [`components/confirmation-modal.md`](components/confirmation-modal.md) | `16570:2642` |

> **Note.** `Modal` heeft geen Uses-vermelding omdat hij geen project-componenten
> importeert (alleen `react-modal` library en `CloseIcon`). Third-party imports
> tellen niet voor Uses.

---

## Status

| Status | Betekenis |
|---|---|
| ✓ | Volledig gespecificeerd, gebouwd, en gebruikt in productie |
| 🚧 | In ontwikkeling — spec aanwezig, code in progress |
| 📋 | Spec aanwezig, code nog niet gestart |
| `[TODO]` | Spec ontbreekt — moet worden ingevuld voor mapping werkt |
