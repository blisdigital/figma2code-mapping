# ConfirmationModal — Example

> **Filled example from a React + MUI + Emotion project.** Adopt the **structure** (sections, mapping tables, drift format), not the values or imports — those are project-specific. Use `templates/component-spec.md` for your own project.

Confirmation modal with title + message + 2 CTAs (Cancel left, primary action right) + optional loading state on the primary CTA. Wrapper on top of the generic `Modal`.

**Figma:** frame `16599:2645` ([link](https://www.figma.com/...)) — frame, no Figma master.

**Location in codebase:** `src/components/ui/confirmation-modal/`

**Type:** organism

## Composition

| Component | Location |
|---|---|
| `Modal` | `src/components/ui/modal/` |
| `SecondaryButton` | `src/components/ui/button/` |
| `Button` | `src/components/ui/button/` |

**Uses:** Modal, Button, SecondaryButton

## Example

```tsx
<ConfirmationModal isOpen={isOpen} onClose={onClose} onConfirm={onConfirm}
  title="Reset password" message="Are you sure..."
  confirmLabel="Reset password" isLoading={isResetting} />
```

## Props

| Prop | Type | Default | Description |
|---|---|---|---|
| `isOpen` | `boolean` | — | Whether the modal is shown |
| `onClose` | `() => void` | — | Click on X / Cancel / esc / overlay |
| `onConfirm` | `() => void` | — | Click on primary action button |
| `title` | `string` | — | Title |
| `message` | `string` | — | Body text |
| `confirmLabel` | `string` | — | Label for primary button |
| `isLoading` | `boolean` | `false` | Disabled primary + label swap to `"Loading..."` |

## Mapping: Figma → Code

### Modal frame (background, container)

The frame is a rounded white container with drop shadow. Rendered via `Modal` (see modal.md for container mapping). ConfirmationModal adds no container styling.

### Header — title + close

Rendered via `Modal` (see modal.md Header mapping). ConfirmationModal supplies only the `title` prop.

### Body — message text

`<p css={messageStyles}>{message}</p>` inside `<ModalInner>`. Styling from `confirmation-modal.styles.ts`.

| Property | Code value | Token / Source |
|---|---|---|
| Margin | `0` | hardcoded (reset) |
| Color | `#1D2632` | `theme.neutral.N100` |
| Font-size | `12px` | `theme.fontSizes.xs` |
| Font-weight | `500` | `theme.fontWeights.regular` (visually "Medium") |
| Line-height | `16px` | hardcoded (matches `theme.lineHeights.xs`) |

### Footer — Cancel + primary

`<ModalFooter>` with `actions={<><SecondaryButton/><Button/></>}` and `hideFooterBorder={true}`.

**Cancel button:**

| Property | Code value | Token / Source |
|---|---|---|
| Component | `<SecondaryButton onClick={onClose}>` | see secondary-button.md |
| Label | `"Cancel"` | hardcoded |

**Primary action button:**

| Property | Code value | Token / Source |
|---|---|---|
| Component | `<Button variant="contained" color="highlight">` | see button.md |
| Label | `props.confirmLabel` (or `"Loading..."` when isLoading) | dynamic |
| Disabled state | `disabled={isLoading}` | code |

### Variant mapping

| Figma context | Code component | Code props |
|---|---|---|
| Reset password modal frame `16599:2645` | `ConfirmationModal` | `title="..." message="..." confirmLabel="..."` |

## Drift notes

> **Spec last validated:** 2026-05-05 (A6 walked through). Code files in sync (hash `8d401e0...`). Cache verified via live MCP.

- **value-mismatch [Major][DEV]** — `modal.tsx:49` Close-icon size hardcoded `16×16` vs Figma instance `24×24` (Δ8). Icon table: Δ >4px = Major. Affects tap target.
- **token-mismatch [Minor][DEV+DESIGNER]** — `theme/tokens.ts` `shadows.lg` rgba `(29,38,50,0.20)` vs Figma `(135,173,187,0.20)`. Shadow table: color difference = Minor.
