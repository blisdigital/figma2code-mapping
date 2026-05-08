# Components — index

Index of all documented components in this project, classified according to
atomic design.

> **Source-of-truth allocation.** Code is source of truth — actual implementation
> lives in `src/components/`. This index shows *what* exists, *what-uses-what*, and
> which Figma node belongs to it.

## Atomic design — five levels (organic)

Brad Frost atomic-design has five levels. This index grows organically — only
levels with actual code-components in this project appear below. Categories
with zero entries are removed.

- **Atoms** — indivisible (Button, Input, Icon, Badge)
- **Molecules** — compositions of atoms with one shared purpose
- **Organisms** — complex compositions with own state, scroll, or keyboard navigation
- **Templates** *(if `src/templates/` or similar exists)* — layout skeleton without content (AppShell, ErrorLayout, DashboardLayout)
- **Pages** *(if `src/pages/` or `app/(routes)/*.tsx` exports page-components)* — concrete page instances (NotFoundPage, UserDashboardPage)

If a project has no Templates or Pages as code-components, those sections do
not appear in this doc. Force no empty categories.

## Selection order — highest level that fits

When mapping a Figma frame to code: **prefer Pages > Templates > Organisms >
Molecules > Atoms.** Do not combine atoms when a higher-order component
already exists.

## Writing convention

- Components in PascalCase
- Per component: name, `Uses`, one-sentence description, link to detail spec, Figma reference
- `Uses` contains only direct imports of **internal components**, no recursive
  expansion, no external libraries (those go in the component spec)
- Atoms always have `Uses: —`

---

## Atoms

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| | — | | | |

## Molecules

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| | | | | |

## Organisms

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| | | | | |

<!--
## Templates (only if project has Templates as code-components)

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| | | | | |

## Pages (only if project has Pages as code-components)

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| | | | | |
-->

> **Note.** Uncomment Templates / Pages sections only when the codebase has
> them as actual exported components. Force no empty categories.
