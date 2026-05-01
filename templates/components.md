# Components — index

Index van alle gedocumenteerde componenten in dit project, geclassificeerd volgens
atomic design. Voor mapping vanuit Figma terug naar code zie de
**[Figma-node index](#figma-node-index)** onderaan.

> **Bron-relatie.** Code is source of truth — daadwerkelijke implementatie staat in
> `src/components/`. Deze index toont *wat* er bestaat, *wat-gebruikt-wat*, en welke
> Figma-node erbij hoort. Twee navigatie-richtingen:
>
> 1. **Code → Figma:** lees de tabellen per atomic-niveau hieronder
> 2. **Figma → Code:** gebruik de [Figma-node index](#figma-node-index)

## Atomic design — drie niveaus

**Atoms** zijn onsplitsbaar. Knoppen, inputs, iconen, labels, badges.

**Molecules** zijn samenstellingen van atoms met één gedeeld doel.

**Organisms** zijn complexe samenstellingen met eigen state, scroll-gedrag, of
toetsenbord-navigatie.

## Selectie-volgorde — organisms eerst

Bij het mappen van een Figma-frame naar code: **check eerst of een organism past, dan
molecules, dan atoms.** Combineer geen atoms als er al een hogere-orde component
bestaat.

Reden: hogere-orde componenten dragen al state en composities. Atoms recombineren
leidt tot inconsistentie.

## Schrijfconventie

- Componenten in PascalCase
- Per component: naam, `Uses`, één-zinnige beschrijving, link naar detail-spec, Figma-referentie
- `Uses` bevat alleen directe imports van **interne componenten**, geen recursieve
  uitwerking, geen externe libraries (die staan in de component-spec)
- Atoms hebben altijd `Uses: —`
- **Figma-kolom**: kort label (master / frame / pending) — voor details zie [Figma-node index](#figma-node-index)

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

---

## Figma-node index

Reverse lookup: gegeven een Figma-node-id, welke code-component is dat?

> **Note.** Niet alle code-componenten hebben een Figma-master. Wanneer code een
> reusable abstractie heeft maar Figma alleen een ad-hoc frame: zie tabel "Frames
> mapped to component". Wanneer Figma wél een master heeft maar die master nog niet
> expliciet via MCP is opgehaald: zie tabel "Instances seen — masters pending
> verification".

### Masters — verified

Figma-componenten (masters) waarvan de node-id en de mapping naar code zijn bevestigd
via een directe MCP-fetch op die master.

| Master node-id | Naam in Figma | → Code-component | Notes |
|---|---|---|---|
| | | | |

### Frames mapped to code-component (figma-master-missing)

Figma-frames zonder master die toch zijn gemapt op een code-component omdat code de
abstractie wel heeft. Drift-flag: `figma-master-missing`.

| Frame node-id | Naam in Figma | → Code-component | Page-context |
|---|---|---|---|
| | | | |

### Instances seen — masters pending verification

Figma-instances die zijn tegengekomen op gemapte pagina's. De master-id is afgeleid
uit het instance-id-formaat (`I<frame-id>;<master-id>`). De master zelf is nog
**niet** via een directe MCP-fetch op die master geverifieerd — dat gebeurt wanneer
die master-node aan bod komt in een mapping-pass.

| Instance node-id | Master (afgeleid) | → Code-component (vermoedelijk) | Waar gezien |
|---|---|---|---|
| | | | |

### Pages mapped

Pagina-frames die voor mapping zijn gebruikt. Deze frames bevatten de bovenstaande
instances/frames als content.

| Page node-id | Naam | Mapping-status |
|---|---|---|
| | | |
