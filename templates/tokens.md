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

## Project styling stack

**[REQUIRED — fill before first map]**

> **Mapping fact, not implementation rule.** This documents *what the codebase
> uses*. The sister [`figma-to-code-implement`](https://github.com/blisdigital/figma2code-implement)
> skill consumes this to enforce single-API styling at code-emit time.
> Mapping documents; implementation enforces.
>
> **Why mandatory:** without this, downstream emit produces dual-styling output
> (e.g., `className` strings in an Emotion-only codebase), which is expensive
> to refactor. `map X` halts before A2 if the `[REQUIRED]` marker is still
> here; A6 check 0b verifies it stays filled on every pass.

- **API:** [e.g., Emotion `styled()` + `css={}`, or Tailwind classes only, or CSS modules, or styled-components]
- **Theme access:** [e.g., `import theme from 'theme'` → `theme.X`, or `var(--name)` from `:root`, or `tw-prefix-X`]
- **Not used:** [explicit list — e.g., "className-direct, inline-styles, Tailwind, styled-components, CSS modules" — prevents parallel paradigms in downstream emit]

If the project mixes APIs (e.g., legacy CSS modules alongside new Emotion), document
both and mark which is canonical for new work. Remove the `[REQUIRED]` marker once filled.

---

## Auto-layout conventions (optional)

> **Optional section.** Fill this in only if the project has a consistent convention
> for translating Figma's auto-layout primitives (fill, hug, direction, gap) to code.
> If patterns are ad-hoc or inconsistent, leave this section empty / remove. Forced
> documentation of inconsistent patterns creates maintenance overhead.

> **Mapping fact, not implementation rule.** Documents the project's translation of
> Figma's structural intent. Token values (gap-8, padding-16) are already in the
> token tables above; this section captures the *semantic-intent translation* (fill,
> hug, direction) that does not map to a single token.

| Figma | Code expression |
|---|---|
| Direction: vertical | [e.g. `flex flex-col`, or `<Stack>`, or `display: flex; flex-direction: column`] |
| Direction: horizontal | [e.g. `flex flex-row`, or `<Inline>`] |
| Fill (primary axis) | [e.g. `flex-1`, or `flex: 1`, or `<Box flex={1}>`] |
| Hug | [e.g. `w-fit h-fit`, or natural sizing] |
| Wrap | [e.g. `flex-wrap`, or `<Stack wrap>`] |
| Distribute: space-between | [e.g. `justify-between`] |
| Align: center | [e.g. `items-center`] |

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
