# Tokens — Voorbeeld (Blis ConfirmationModal scope)

> **Dit is een ingevuld voorbeeld** ter referentie voor hoe een werkende `tokens.md`
> eruitziet in een echt project (Blis, React + Emotion + TypeScript theme-object).
> Kopieer niet rechtstreeks — gebruik `templates/tokens.md` voor je eigen project.

## Bron-relatie

Code is source of truth. Tokens leven in `src/theme/tokens.ts` + `src/theme/colors.ts`,
gebundeld via `src/theme/index.ts` als TypeScript theme-object.

## Format

TypeScript theme-object. Code-pad is `theme.<categorie>.<token>`.

## Schrijfconventie

| Kolom | Inhoud |
|---|---|
| **Code-pad** | `theme.fontSizes.xs` |
| **Figma-naam** | `Color/Brand/P500` of `—` of `[VERIFY]` |
| **Waarde** | De actuele waarde |
| **Gebruik** | Korte uitleg |

---

## Color

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.core.white` | `White` | `#FFFFFF` | Modal-achtergrond, button-achtergrond |
| `theme.gray[100]` | `[VERIFY]` | `#1D2632` | Body text, secondary button text |
| `theme.gray[60]` | `[VERIFY]` | `#777D84` | CloseIcon fill, CloseButton focus-outline |
| `theme.blue[120]` | `[VERIFY]` | `#316A7D` | ModalH3 styled component (niet door Modal-wrapper gebruikt) |
| `theme.blue[40]` | `[VERIFY]` | `#C5DBE3` | ModalFooter borderTop wanneer niet hidden |
| `theme.blue[30]` | `Blue/B30` | `#D3E4EA` | Modal mobile borderTop, SecondaryButton border |
| `theme.yellow[100]` | `Yellow/Y100` | `#FFCD00` | Highlight via theme.highlight |

> **Note bij `theme.neutral.N100`** in `confirmation-modal_styles.ts`: deze import
> bestaat niet in de gevonden colors.ts. Code zou faillen tenzij `neutral` elders is
> gedefinieerd. **DRIFT: hardcoded-value** — waarde is `#1D2632`, identiek aan
> `theme.gray[100]`. Verifiëren of dit een import-typo is of een aparte palette.

## Spacing

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.spacing.xs` | `[VERIFY]` | `4px` | CloseButton padding |
| `theme.spacing.sm` | `[VERIFY]` | `8px` | ModalFooter gap, gap binnen Button |
| `theme.spacing.lg` | `[VERIFY]` | `16px` | ModalFooter padding-top, button medium-padding-x |
| `theme.spacing.xl` | `[VERIFY]` | `24px` | Modal padding-vertical, ModalFooter padding-right |
| `theme.spacing.xxl` | `[VERIFY]` | `32px` | Modal padding-horizontal |

## Radius

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.radius.sm` | `[VERIFY]` | `4px` | CloseButton focus-outline border-radius |
| `theme.radius.lg` | `[VERIFY]` | `16px` | Modal border-radius (desktop) |
| `theme.radius.xxxl` | `[VERIFY]` | `40px` | Button border-radius |

## Shadow

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.shadows.lg` | `[VERIFY]` | `0 4px 16px 0 rgba(29, 38, 50, 0.20)` | Modal boxShadow |

## Typography

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.fontSizes.xs` | `[VERIFY]` | `12px` | Body text in ConfirmationModal, button text |
| `theme.lineHeights.xs` | `[VERIFY]` | `16px` | Past bij `fontSizes.xs` |
| `theme.fontWeights.regular` | `[VERIFY]` | `500` | Body text weight |
| `theme.fontWeights.semibold` | `[VERIFY]` | `600` | Button text weight |

## Z-index

| Code-pad | Figma-naam | Waarde | Gebruik |
|---|---|---|---|
| `theme.zIndex.modal` | — | `101` | Modal overlay |

---

## Impliciete semantic-laag

| Code-pad | Verwijst naar | Gebruik |
|---|---|---|
| `theme.highlight` | `yellow[100]` | Primary action color |
| `theme.primary` | `gray[100]` | Primary text |
| `theme.danger` | `red[100]` | Destructive |
| `theme.success` | `green[100]` | Success |
| `theme.contentBlock` | `core.white` | Card/modal achtergrond |

> **Note.** Niet forced uitgebreid. Wanneer een nieuwe component een betekenis-laag
> nodig heeft die niet bestaat, voeg een alias toe in `theme/index.ts`.

---

## Sync log

| Datum | Wijziging | Reden |
|---|---|---|
| `2026-04-30` | Initial fill bij ConfirmationModal documentatie | Eerste pass workflow |
