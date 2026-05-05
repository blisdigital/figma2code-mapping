# ConfirmationModal — Voorbeeld

> **Ingevuld voorbeeld uit een React + MUI + Emotion project.** Adopteer de **structuur** (secties, mapping-tabellen, drift-format), niet de waarden of imports — die zijn project-specifiek. Gebruik `templates/component-spec.md` voor je eigen project.

Bevestigings-modal met titel + message + 2 CTAs (Annuleren links, primary actie rechts) + optionele loading-state op de primary CTA. Wrapper bovenop generieke `Modal`.

**Figma:** frame `16599:2645` ([link](https://www.figma.com/...)) — frame, geen Figma-master.

**Locatie in codebase:** `src/components/ui/confirmation-modal/`

**Type:** organism

## Compositie

| Component | Locatie |
|---|---|
| `Modal` | `src/components/ui/modal/` |
| `SecondaryButton` | `src/components/ui/button/` |
| `Button` | `src/components/ui/button/` |

**Uses:** Modal, Button, SecondaryButton

## Voorbeeld

```tsx
<ConfirmationModal isOpen={isOpen} onClose={onClose} onConfirm={onConfirm}
  title="Wachtwoord resetten" message="Weet je zeker..."
  confirmLabel="Wachtwoord resetten" isLoading={isResetting} />
```

## Props

| Prop | Type | Default | Beschrijving |
|---|---|---|---|
| `isOpen` | `boolean` | — | Of de modal getoond wordt |
| `onClose` | `() => void` | — | Klik op X / Cancel / esc / overlay |
| `onConfirm` | `() => void` | — | Klik op primary action-knop |
| `title` | `string` | — | Titel |
| `message` | `string` | — | Body-tekst |
| `confirmLabel` | `string` | — | Label voor primary knop |
| `isLoading` | `boolean` | `false` | Disabled primary + label-swap naar `"Bezig..."` |

## Mapping: Figma → Code

### Modal-frame (achtergrond, container)

Frame is een afgerond witte container met drop-shadow. Gerenderd via `Modal` (zie modal.md voor container-mapping). ConfirmationModal voegt geen container-styling toe.

### Header — title + close

Gerenderd via `Modal` (zie modal.md Header-mapping). ConfirmationModal levert alleen de `title`-prop.

### Body — message-tekst

`<p css={messageStyles}>{message}</p>` binnen `<ModalInner>`. Styling uit `confirmation-modal.styles.ts`.

| Eigenschap | Code-waarde | Token / Bron |
|---|---|---|
| Margin | `0` | hardcoded (reset) |
| Kleur | `#1D2632` | `theme.neutral.N100` |
| Font-size | `12px` | `theme.fontSizes.xs` |
| Font-weight | `500` | `theme.fontWeights.regular` (visueel "Medium") |
| Line-height | `16px` | hardcoded (matcht `theme.lineHeights.xs`) |

### Footer — Cancel + primary

`<ModalFooter>` met `actions={<><SecondaryButton/><Button/></>}` en `hideFooterBorder={true}`.

**Cancel-knop:**

| Eigenschap | Code-waarde | Token / Bron |
|---|---|---|
| Component | `<SecondaryButton onClick={onClose}>` | zie secondary-button.md |
| Label | `"Annuleer"` | hardcoded |

**Primary action-knop:**

| Eigenschap | Code-waarde | Token / Bron |
|---|---|---|
| Component | `<Button variant="contained" color="highlight">` | zie button.md |
| Label | `props.confirmLabel` (of `"Bezig..."` bij isLoading) | dynamic |
| Disabled-state | `disabled={isLoading}` | code |

### Variant-mapping

| Figma context | Code-component | Code-props |
|---|---|---|
| Reset password modal-frame `16599:2645` | `ConfirmationModal` | `title="..." message="..." confirmLabel="..."` |

## Drift-aandachtspunten

> **Spec laatst gevalideerd:** 2026-05-05 (A6 doorlopen). Code-files in sync (hash `8d401e0...`). Cache via live MCP geverifieerd.

- **value-mismatch [Major][DEV]** — `modal.tsx:49` Close-icon size hardcoded `16×16` vs Figma instance `24×24` (Δ8). Iconen-tabel: Δ >4px = Major. Beïnvloedt tap-target.
- **token-mismatch [Minor][DEV+DESIGNER]** — `theme/tokens.ts` `shadows.lg` rgba `(29,38,50,0.20)` vs Figma `(135,173,187,0.20)`. Shadow-tabel: kleur-verschil = Minor.
