# Components — index

Index of all documented components in this project, classified according to
atomic design.

> **Source-of-truth allocation.** Code is source of truth — actual implementation
> lives in `src/components/`. This index shows *what* exists, *what-uses-what*, and
> which Figma node belongs to it.

## Atomic design — three levels

**Atoms** are indivisible. Buttons, inputs, icons, labels, badges.

**Molecules** are compositions of atoms with one shared purpose.

**Organisms** are complex compositions with their own state, scroll behavior, or
keyboard navigation.

## Selection order — organisms first

When mapping a Figma frame to code: **first check whether an organism fits, then
molecules, then atoms.** Do not combine atoms when a higher-order component
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
