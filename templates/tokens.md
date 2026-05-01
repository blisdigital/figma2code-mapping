# Tokens

Code-tokens met expliciete Figma-mapping per rij. Dit document is de **driekoppige
mapping** tussen Figma-variabelen en code-pad (en de actuele waarde).

> **Bron-relatie.** Code is source of truth. Tokens leven in code (CSS custom
> properties, TypeScript theme-object, Tailwind config — afhankelijk van project).
> Deze markdown is de mens- en agent-leesbare mapping tussen Figma en code.

## Format past zich aan aan codebase

Documenteer wat de codebase heeft. Forceer geen vorm. Drie veelvoorkomende vormen:

**CSS custom properties:** code-pad in tabel `--color-primary`
**TypeScript theme-object:** code-pad `theme.primary`, `theme.spacing.lg`
**Tailwind utility:** code-pad `bg-primary`, `text-primary`

## Schrijfconventie per rij

| Kolom | Inhoud |
|---|---|
| **Code-pad** | Zoals in components gebruikt |
| **Figma-naam** | Zoals in Figma als variabele bestaat, of `—`, of `[VERIFY]` indien onbevestigd |
| **Waarde** | De actuele waarde |
| **Gebruik** | Korte uitleg wanneer dit token gebruiken |

## Aanpak bij eerste pass

Begin niet met de hele tokens-laag. Documenteer alleen tokens die het eerste component
raakt. Volgende componenten breiden de tabellen uit.

---

## Color

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| | | | |

## Spacing

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| | | | |

## Radius

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| | | | |

## Shadow

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| | | | |

## Typography

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| | | | |

---

## Impliciete semantic-laag

Sommige codebases hebben geen formele primitive/semantic-split, maar gebruiken wel
aliassen die als semantic functioneren. Lijst hier.

| Code-pad | Verwijst naar | Gebruik |
|---|---|---|
| | | |

> **Note.** Verwijder deze sectie als jouw codebase geen impliciete semantic-laag
> heeft. Geen forced uitbreiding.

---

## Sync log

| Datum | Wijziging | Reden |
|---|---|---|
| | | |
