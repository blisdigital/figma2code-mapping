# Components — index

Index van alle gedocumenteerde componenten in dit project, geclassificeerd volgens
atomic design.

> **Bron-relatie.** Code is source of truth — daadwerkelijke implementatie staat in
> `src/components/`. Deze index toont *wat* er bestaat, *wat-gebruikt-wat*, en welke
> Figma-node erbij hoort.

## Atomic design — drie niveaus

**Atoms** zijn onsplitsbaar. Knoppen, inputs, iconen, labels, badges.

**Molecules** zijn samenstellingen van atoms met één gedeeld doel.

**Organisms** zijn complexe samenstellingen met eigen state, scroll-gedrag, of
toetsenbord-navigatie.

## Selectie-volgorde — organisms eerst

Bij het mappen van een Figma-frame naar code: **check eerst of een organism past, dan
molecules, dan atoms.** Combineer geen atoms als er al een hogere-orde component
bestaat.

## Schrijfconventie

- Componenten in PascalCase
- Per component: naam, `Uses`, één-zinnige beschrijving, link naar detail-spec, Figma-referentie
- `Uses` bevat alleen directe imports van **interne componenten**, geen recursieve
  uitwerking, geen externe libraries (die staan in de component-spec)
- Atoms hebben altijd `Uses: —`

---

## Atoms

| Component | Uses | Beschrijving | Detail | Figma |
|---|---|---|---|---|
| | — | | | |

## Molecules

| Component | Uses | Beschrijving | Detail | Figma |
|---|---|---|---|---|
| | | | | |

## Organisms

| Component | Uses | Beschrijving | Detail | Figma |
|---|---|---|---|---|
| | | | | |
