# [ComponentNaam]

[Eén-zinnige beschrijving van wat het component is en doet.]

**Wanneer gebruiken.** [Bij welke Figma-frames / use-cases dit component gebruiken.
Inclusief wanneer **niet** te gebruiken — dat voorkomt verkeerde selectie.]

**Figma:** `[node-id]` ([link](https://www.figma.com/...))

**Locatie in codebase:** `src/components/...`

**Type:** atom | molecule | organism

## Compositie

[Voor molecules en organisms: welke componenten gebruikt dit?]

| Component | Rol | Locatie |
|---|---|---|
| `[Naam]` | [Wat doet het in deze compositie] | `src/components/...` |

**Uses:** [Komma-gescheiden lijst van directe component-imports. Atoms hebben hier `—`.]

## Voorbeeld

```tsx
// Code-snippet die toont hoe dit component gebruikt wordt
```

## Props

| Prop | Type | Default | Beschrijving |
|---|---|---|---|
| `[naam]` | `[type]` | `[default]` | [Wat doet deze prop] |

## Wat dit component toevoegt

[Voor wrappers en composed components: wat doet déze component bovenop de
wrapper-component(en)? Bijvoorbeeld: ConfirmationModal voegt loading-state, hardcoded
twee-knop-structuur, en `hideFooterBorder` default toe aan de generieke Modal.]

## Mapping: Figma → Code

[Per sub-element: korte prosa-beschrijving + tabel met de mapping. Geef de mens een
leesbare uitleg, geef de agent een scanbare tabel.]

### [Sub-element naam]

[Eén alinea prosa: waar komt dit vandaan in code, wat doet het visueel, welke tokens
worden gebruikt.]

| Eigenschap | Code-waarde | Token / Bron |
|---|---|---|
| [...] | [...] | [...] |

### [Volgend sub-element]

[Idem.]

## Edge cases

[Bekende randgevallen die de implementatie afdekt of waar gebruikers op moeten letten.]

## Drift-aandachtspunten

[Optioneel — alleen invullen wanneer er bekende drift is tussen Figma en code die de
gebruiker moet weten. Eén regel per drift-punt, niet uitgebreid. Kan weggelaten worden
als er geen drift is.]
