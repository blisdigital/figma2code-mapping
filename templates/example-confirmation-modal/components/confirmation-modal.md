# ConfirmationModal — Voorbeeld (Blis)

> **Dit is een ingevuld voorbeeld** ter referentie. Kopieer niet rechtstreeks — gebruik
> `templates/component-spec.md` voor je eigen project.

Modale dialoog voor het bevestigen van een actie met twee uitkomsten: annuleren of
bevestigen. Wrapper rondom de generieke `Modal`-component met voorgedefinieerde
structuur (titel, message, twee knoppen) en loading-state-handling op de bevestig-knop.

**Wanneer gebruiken.** Bij Figma-frames die een actie-bevestiging tonen met titel +
message + twee knoppen. **Niet** voor modals met formulieren, multi-step flows, of
complexe content — gebruik dan `Modal` direct.

**Figma:** `16570:2642`

**Locatie in codebase:** `src/components/ui/confirmation-modal/`

**Type:** organism

## Compositie

| Component | Rol | Locatie |
|---|---|---|
| `Modal` | Generieke wrapper — header, body, footer-slot, sluit-gedrag | `src/components/ui/modal/` |
| `Button` (`color="highlight"`) | Filled confirm-knop | `src/components/ui/button/` |
| `SecondaryButton` | Outlined cancel-knop | `src/components/ui/button/` |

**Uses:** Modal, Button, SecondaryButton

## Voorbeeld

```tsx
<ConfirmationModal
  isOpen={isModalOpen}
  onClose={() => setIsModalOpen(false)}
  onConfirm={handleConfirm}
  title="Creditbundel aanvragen"
  message="Weet je zeker dat je Creditbundel [Size] wilt aanvragen?"
  confirmLabel="Aanvragen"
  isLoading={isSubmitting}
/>
```

## Props

| Prop | Type | Default | Beschrijving |
|---|---|---|---|
| `isOpen` | `boolean` | — | Modal zichtbaar |
| `onClose` | `() => void` | — | Sluit-callback |
| `onConfirm` | `() => void` | — | Bevestig-callback |
| `title` | `string` | — | Header-titel |
| `message` | `string` | — | Body-tekst (in `<p>`) |
| `confirmLabel` | `string` | — | Label van de bevestig-knop |
| `isLoading` | `boolean` | `false` | Disabled confirm + label `"Bezig..."` |

## Wat dit component toevoegt

ConfirmationModal is een smalle wrapper rond Modal. Hij doet drie dingen:

1. Hardcodet de footer-actions naar [SecondaryButton + Button] in die volgorde
2. Voegt loading-state toe (disabled + label-swap naar `"Bezig..."`)
3. Zet `hideFooterBorder` standaard aan

## Mapping: Figma → Code

### Modal-container

Komt uit Modal-component. Op desktop gecentreerd met vaste breedte en afgeronde hoeken.

| Eigenschap | Code-waarde | Token |
|---|---|---|
| Width (desktop) | `604px` | hardcoded |
| Padding (vertical) | `theme.spacing.xl` (24px) | tokens.md |
| Padding (horizontal) | `theme.spacing.xxl` (32px) | tokens.md |
| Border-radius | `theme.radius.lg` (16px) | tokens.md |
| Background | `'white'` | `theme.white` |
| Box-shadow | `theme.shadows.lg` | tokens.md |

### Body

`<p>` met `messageStyles`.

| Eigenschap | Code-waarde | Token |
|---|---|---|
| Color | `#1D2632` | `theme.neutral.N100` |
| Font-size | `12px` | `theme.fontSizes.xs` |
| Line-height | `'16px'` (hardcoded) | identiek aan `theme.lineHeights.xs` |
| Font-weight | `500` | `theme.fontWeights.regular` |

### Buttons

Confirm: `<Button color="highlight" variant="contained">`. Cancel: `<SecondaryButton>`.

| Eigenschap | Code-waarde | Token |
|---|---|---|
| Confirm background | `#FFCD00` | `theme.highlight` |
| Confirm height | `38px` | hardcoded |
| Cancel background | `#FFFFFF` | `theme.white` |
| Cancel border | `1px solid #D3E4EA` | `theme.blue[30]` |

## Edge cases

- `isLoading=true`: confirm-knop disabled, label `"Bezig..."`. SecondaryButton blijft
  actief — annuleren tijdens laden is mogelijk.
- Lange title of message: Modal heeft `maxHeight: '80vh'` met `overflow: 'auto'` op
  desktop.

## Drift-aandachtspunten

- **Body color** gebruikt `theme.neutral.N100` maar `colors.ts` heeft geen `neutral`
  export. Code zou faillen of `neutral` is elders gedefinieerd. Verifiëren.
- **Hardcoded `lineHeight: '16px'`** is identiek aan `theme.lineHeights.xs` — quick
  win om te vervangen.
- **`"Bezig..."`** is hardcoded NL. Indien i18n nodig: extraheren naar prop.
