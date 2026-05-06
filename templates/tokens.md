# Tokens

Code tokens with explicit Figma mapping per row. This document is the **three-column
mapping** between Figma variables and code path (and the actual value).

> **Source-of-truth allocation.** Code is source of truth. Tokens live in code (CSS
> custom properties, TypeScript theme object, Tailwind config — depending on project).
> This markdown is the human- and agent-readable mapping between Figma and code.

## Format adapts to the codebase

Document what the codebase has. Do not force a form. Three common forms:

**CSS custom properties:** code path in table `--color-primary`
**TypeScript theme object:** code path `theme.primary`, `theme.spacing.lg`
**Tailwind utility:** code path `bg-primary`, `text-primary`

## Writing convention per row

| Column | Content |
|---|---|
| **Code path** | As used in components |
| **Figma name** | As it exists in Figma as a variable, or `—`, or `[VERIFY]` if unconfirmed |
| **Value** | The actual value |
| **Use** | Short explanation when to use this token |

## First-pass approach

Do not start with the entire token layer. Document only tokens that the first component
touches. Subsequent components extend the tables.

---

## Color

| Code path | Figma name | Value | Use |
|---|---|---|---|
| | | | |

## Spacing

| Code path | Figma name | Value | Use |
|---|---|---|---|
| | | | |

## Radius

| Code path | Figma name | Value | Use |
|---|---|---|---|
| | | | |

## Shadow

| Code path | Figma name | Value | Use |
|---|---|---|---|
| | | | |

## Typography

| Code path | Figma name | Value | Use |
|---|---|---|---|
| | | | |

---

## Implicit semantic layer

Some codebases have no formal primitive/semantic split, but use aliases that function
as semantic tokens. List them here.

| Code path | Refers to | Use |
|---|---|---|
| | | |

> **Note.** Remove this section if your codebase has no implicit semantic layer.
> No forced expansion.

---

## Sync log

| Date | Change | Reason |
|---|---|---|
| | | |
