# [ComponentNaam]

[Eén-zinnige technische beschrijving: wat is het component, op welke library/wrapper baseert het.]

**Figma:** `[node-id]` ([link](https://www.figma.com/...)) — master | frame | géén master

**Locatie in codebase:** `src/components/...`

**Type:** atom | molecule | organism

## Compositie

| Component | Locatie |
|---|---|
| `[Naam]` | `src/components/...` of `[extern]` |

**Uses:** [Komma-gescheiden lijst van directe component-imports — alleen interne. Atoms hebben hier `—`.]

## Voorbeeld (optioneel)

```tsx
// Eén canonical use, max 5 regels. Weglaten als triviaal.
```

## Props

[Compact — alleen WorQX-specifieke props. MUI-doorgeleide props weglaten tenzij default afwijkt.]

| Prop | Type | Default | Beschrijving |
|---|---|---|---|
| `[naam]` | `[type]` | `[default]` | [korte beschrijving — wat het instelt, geen gedrag] |

## Mapping: Figma → Code

[Per sub-element één korte alinea (max 3 regels) + tabel. Géén edge cases, géén gedrag, géén "wanneer gebruiken".]

### [Sub-element naam]

[Korte mapping-context — waar in code, welke tokens. Max 3 regels.]

| Eigenschap | Code-waarde | Token / Bron |
|---|---|---|
| [...] | [...] | [...] |

### Variant-mapping (alleen als component variants of states heeft)

[Disambiguatie voor MCP: welke Figma-variant matcht welk code-resultaat.]

| Figma variant / state | Code-component | Code-props |
|---|---|---|
| `[bv. primary]` | `[bv. Button]` | `[bv. variant="contained" color="highlight"]` |

## Drift-aandachtspunten

[Optioneel — alleen invullen wanneer er drift is die de drift-test passeert (zou MCP-codegen vanuit Figma een visueel verkeerd resultaat geven? Ja → drift). `[VERIFY]`-items, hardcoded-met-juiste-waarde, en code-only-abstractie-administratie horen NIET hier — die gaan naar `verify-queue.md` of nergens. Eén regel per drift.]

> **Spec laatst gevalideerd:** [datum]. Code-files in sync (hash `[...]`). [Optioneel: cache-status MCP.]

- [drift-type] [Severity][Owner] — [bestand:regel] [wat verschilt]. Actie: [wat te doen].
