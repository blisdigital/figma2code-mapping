# Tokens — Example (Blis ConfirmationModal scope)

> **This is a filled example** for reference, showing what a working `tokens.md`
> looks like in a real project (Blis, React + Emotion + TypeScript theme object).
> Do not copy directly — use `templates/tokens.md` for your own project.

## Source-of-truth allocation

Code is source of truth. Tokens live in `src/theme/tokens.ts` + `src/theme/colors.ts`,
bundled via `src/theme/index.ts` as a TypeScript theme object.

## Format

TypeScript theme object. Code path is `theme.<category>.<token>`.

## Writing convention

| Column | Content |
|---|---|
| **Code path** | `theme.fontSizes.xs` |
| **Figma name** | `Color/Brand/P500` or `—` or `[VERIFY]` |
| **Value** | The actual value |
| **Use** | Short explanation |

---

## Color

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.core.white` | `White` | `#FFFFFF` | Modal background, button background |
| `theme.gray[100]` | `[VERIFY]` | `#1D2632` | Body text, secondary button text |
| `theme.gray[60]` | `[VERIFY]` | `#777D84` | CloseIcon fill, CloseButton focus outline |
| `theme.blue[120]` | `[VERIFY]` | `#316A7D` | ModalH3 styled component (not used by Modal wrapper) |
| `theme.blue[40]` | `[VERIFY]` | `#C5DBE3` | ModalFooter borderTop when not hidden |
| `theme.blue[30]` | `Blue/B30` | `#D3E4EA` | Modal mobile borderTop, SecondaryButton border |
| `theme.yellow[100]` | `Yellow/Y100` | `#FFCD00` | Highlight via theme.highlight |

> **Note.** `theme.gray[100]` has the same value (#1D2632) as `theme.neutral.N100` — duplicate in code. For mapping to Figma `Neutral/N100`: use `theme.neutral.N100`. Existing components that use `theme.gray[100]` are not drift, but new code follows the neutral layer.

## Spacing

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.spacing.xs` | `[VERIFY]` | `4px` | CloseButton padding |
| `theme.spacing.sm` | `[VERIFY]` | `8px` | ModalFooter gap, gap inside Button |
| `theme.spacing.lg` | `[VERIFY]` | `16px` | ModalFooter padding-top, button medium-padding-x |
| `theme.spacing.xl` | `[VERIFY]` | `24px` | Modal padding-vertical, ModalFooter padding-right |
| `theme.spacing.xxl` | `[VERIFY]` | `32px` | Modal padding-horizontal |

## Radius

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.radius.sm` | `[VERIFY]` | `4px` | CloseButton focus-outline border-radius |
| `theme.radius.lg` | `[VERIFY]` | `16px` | Modal border-radius (desktop) |
| `theme.radius.xxxl` | `[VERIFY]` | `40px` | Button border-radius |

## Shadow

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.shadows.lg` | `[VERIFY]` | `0 4px 16px 0 rgba(29, 38, 50, 0.20)` | Modal boxShadow |

## Typography

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.fontSizes.xs` | `[VERIFY]` | `12px` | Body text in ConfirmationModal, button text |
| `theme.lineHeights.xs` | `[VERIFY]` | `16px` | Pairs with `fontSizes.xs` |
| `theme.fontWeights.regular` | `[VERIFY]` | `500` | Body text weight |
| `theme.fontWeights.semibold` | `[VERIFY]` | `600` | Button text weight |

## Z-index

| Code path | Figma name | Value | Use |
|---|---|---|---|
| `theme.zIndex.modal` | — | `101` | Modal overlay |

---

## Implicit semantic layer

| Code path | Refers to | Use |
|---|---|---|
| `theme.highlight` | `yellow[100]` | Primary action color |
| `theme.primary` | `gray[100]` | Primary text |
| `theme.danger` | `red[100]` | Destructive |
| `theme.success` | `green[100]` | Success |
| `theme.contentBlock` | `core.white` | Card/modal background |

> **Note.** Not forcibly expanded. When a new component needs a meaning layer
> that does not exist, add an alias in `theme/index.ts`.

---

## Sync log

| Date | Change | Reason |
|---|---|---|
| `2026-04-30` | Initial fill during ConfirmationModal documentation | First-pass workflow |
