# Components — index — Example (Blis)

> **This is a filled example** for reference. Do not copy directly — use
> `templates/components.md` for your own project.

## Atomic design — three levels

**Atoms** are indivisible. **Molecules** are compositions with one purpose.
**Organisms** are complex compositions with state, scroll behavior, or
keyboard handling.

## Selection order — organisms first

When mapping a Figma frame to code: **first check whether an organism fits, then
molecules, then atoms.**

---

## Atoms

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| `Button` | — | Primary button on MUI base, sizes small/medium and color variants (incl. `highlight`). Lift hover and active press, focus-visible 2px white outline | `[TODO]` | `[TODO]` |
| `SecondaryButton` | — | Outlined cancel/secondary button, white background with blue[30] border | `[TODO]` | `[TODO]` |
| `IconButton` | — | Icon-only button, can be rounded (pill) or not (md radius) | `[TODO]` | `[TODO]` |

> **Note.** All three buttons live in `src/components/ui/button/` with shared
> styles. Not expanded as separate specs in this pass — only registered for the
> ConfirmationModal mapping.

## Molecules

*No molecules documented in this pass.*

## Organisms

| Component | Uses | Description | Detail | Figma |
|---|---|---|---|---|
| `Modal` | — | Generic modal wrapper around `react-modal` with header (title + close), body, and optional footer actions. Desktop: 604px centered; mobile: full-screen | `[TODO]` | `[TODO]` |
| `ConfirmationModal` | `Modal`, `Button`, `SecondaryButton` | Confirmation dialog with title, message, cancel + confirm button, and loading state | [`components/confirmation-modal.md`](components/confirmation-modal.md) | `16570:2642` |

> **Note.** `Modal` has no Uses listing because it does not import project
> components (only the `react-modal` library and `CloseIcon`). Third-party imports
> do not count as Uses.

---

## Status

| Status | Meaning |
|---|---|
| ✓ | Fully specified, built, and used in production |
| 🚧 | In development — spec present, code in progress |
| 📋 | Spec present, code not yet started |
| `[TODO]` | Spec missing — must be filled before mapping works |
