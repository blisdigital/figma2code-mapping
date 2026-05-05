---
name: figma-to-code
version: "2.0"
description: >
  Mapt Figma-designs op een bestaande codebase via expliciete documentatie van tokens,
  componenten, en per-component-specs. Gebruik deze skill wanneer de gebruiker zegt
  "documenteer deze component", "map dit Figma-frame", "voeg X toe aan de codebase",
  "werk de tokens bij", of een Figma-link deelt voor implementatie. Ook triggeren
  bij "figma-to-code", "design-to-code", of wanneer de projectrepo een
  figma-to-code-mapping folder heeft met tokens.md en components.md.
  Doel: MCP-codegen vanuit Figma matcht ≥90% met de bestaande codebase, doordat
  tokens en componenten expliciet zijn gemapt op code-paden. Werkt voor elk project
  met een bestaande codebase plus Figma als design-intent.
---

# Figma-to-Code

Mapping-skill die ervoor zorgt dat Figma-MCP-codegen visueel én stylistisch matcht met
de bestaande codebase. Drie expliciete documenten in de projectrepo: `tokens.md`,
`components.md`, en per-component-specs in `components/<naam>.md`.

## Het doel

**Mapping van tokens en componenten tussen Figma en code.** MCP-output gebruikt direct
de juiste code-paden, imports en patronen — geen handmatig vertaalwerk per request.

Ontwikkelaars en designers gebruiken deze docs om de match tussen design en code
hoog te houden. Drift wordt kort gemarkeerd waar zichtbaar; mapping is het hoofdwerk.

## Bron-verdeling

**Figma is intentie.** Wat moet er komen, welke schermen, welke nieuwe componenten.

**Code is waarheid.** Welke tokens en componenten bestaan, met welke waardes.

Bij conflict wint code. Drift wordt gemarkeerd, niet stilzwijgend opgelost.

## Bron-mechanisme

**Cache is de werkende laag.** De agent map't tegen een lokale cache van Figma-node-data.
Conventie: `figma-context/<node-id>.json`, één bestand per node. Mapping leest uit de
cache, niet rechtstreeks uit Figma.

**MCP is het verversmechanisme.** Bij elke mapping-pass: live MCP-fetch, vergelijk met
cache, schrijf nieuwste versie weg, map vanuit de bijgewerkte cache.

**Cache-format met spec-link.** Elk cache-bestand bevat metadata over welke
code-component erbij hoort en wanneer de spec voor het laatst in sync was met de code:

```json
{
  "node_id": "16599:2645",
  "name": "Reset password modal",
  "mapped_to_component": "ConfirmationModal",
  "spec_path": "components/confirmation-modal.md",
  "spec_synced_with_code_at": "2026-05-04T12:00:00Z",
  "spec_synced_with_files_hash": "sha256:abc123..."
}
```

**Hash-check bij elke pass:** agent hashed huidige code-bestanden, vergelijkt met
`spec_synced_with_files_hash`. Bij verschil: spec is out-of-sync sinds code-wijziging.
Voorkomt dat handmatig wordt gewerkt met verouderde mappings.

**Mapping zonder actieve MCP** kan, mits cache aanwezig is — noteer dan dat de cache
niet via MCP is geverifieerd.

**Master-verificatie via instance-id-format.** Wanneer een master-page niet direct
bereikbaar is via MCP (master ligt op andere page in de Figma-file), maar instances
ervan wél bereikbaar zijn via een ander frame: het instance-id-format
`I<frame-id>;<master-id>` is voldoende bewijs voor master-id-verificatie. Master-cache
is niet vereist — instance-rendering binnen een geverifieerd frame levert alle
mapping-data die voor MCP-codegen nodig is. Documenteer in cache-bestand:
`master_verified_via: "instance-id-format"`.

## De documenten

| Document | Doel |
|---|---|
| `docs/tokens.md` | Driekoppige mapping (Figma-naam → code-pad → waarde) |
| `docs/components.md` | Index met Uses-kolom en atomic-design classificatie |
| `docs/components/<naam>.md` | Per component: spec, props, states, mapping naar Figma |

Templates voor deze documenten staan in `templates/`. Bij `setup`-commando worden ze
gekopieerd naar de projectrepo.

## De werkwijze-regel

Voor elke taak, zonder uitzondering:

1. **Lezen voor schrijven.** Lees eerst de relevante documenten. Geen code, geen
   wijziging zonder eerst te lezen.

2. **Documenteer wat is, niet wat zou moeten zijn.** Code is source of truth. Volg
   de codebase, forceer geen abstracties die er niet zijn.

3. **Geen improvisatie bij gaten.** Als iets niet in de documenten staat, stop. Vraag
   de gebruiker. Maak geen aannames.

4. **Bevestiging voor doorvoeren.** Wanneer je een wijziging voorstelt — in code of
   in een document — vraag bevestiging voordat je doorvoert.

5. **Bij selectie van componenten: organisms eerst.** Check eerst of een organism past,
   dan molecules, dan atoms. Combineer geen atoms als er al een hogere-orde component
   bestaat.

6. **Bij `component-missing` — niet auto-genereren.** Markeer alleen; ontwikkelaar
   maakt de code-component voordat mapping mogelijk is.

## Slash-commando's

- `/figma-to-code setup` — vraag bevestiging, dan `docs/`-structuur aanmaken
- `/figma-to-code map <component>` — start mapping van die component (A1-A5)
- `/figma-to-code init-claude-md` — toon markdown-blok om in projectrepo CLAUDE.md te plakken

## Werkwijze A1-A5

### A1. Inventariseer

Scan de bestaande codebase. Identificeer:
- Waar tokens leven (CSS variables, theme-object, Tailwind config, mix)
- Welke component-folders er zijn (`src/components/ui/`, etc.)
- Welk format styles gebruikt (CSS modules, Emotion, styled-components, etc.)

Geef de gebruiker een korte samenvatting voor je doorgaat.

### A2. Vul tokens.md (incrementeel)

Documenteer alleen tokens die het eerste component raakt. Volgende componenten breiden
de tabellen uit.

Per token een rij met:
- Code-pad (hoe je het in components gebruikt)
- Figma-naam (zoals het in Figma als variabele bestaat) of `[VERIFY]` indien onbevestigd
- Waarde
- Gebruik (korte uitleg)

### A3. Documenteer eerste component

Kies samen met de gebruiker één representatieve component. Voor dat component:

1. Lees de implementatie (component-file, styles-file, types-file)
2. Lees de wrapper-laag indien aanwezig
3. Vul `docs/components/<naam>.md` op basis van `templates/component-spec.md`
4. Voeg toe aan `components.md` met Uses-kolom

### A4. Mapping aan Figma

Per element: agent haalt node-data op via cache (refresh via MCP), leest de code, en
stelt een mapping voor (Figma-property → code-token of code-pad). Gebruiker bevestigt.
Bij twijfel: `[VERIFY]` in de Figma-naam-kolom, niet improviseren.

**Verplichte vergelijking per hardcoded waarde.** Voor elke hardcoded waarde in code
(padding, border-radius, height, etc.) direct vergelijken met de Figma-instance-waarde
uit MCP-output. Niet alleen "code-waarde + token" noteren in de tabel — ook expliciet
de drift-test toepassen: matcht de Figma-rendered output? Bij verschil: drift in spec
+ `drifts.md`, niet pas in volgende re-validatie-pass. Drift-detectie hoort bij A4,
niet bij re-validatie.

### A5. Recursief Uses afmaken (zonder aparte permission-vraag)

Na elke component-mapping: scan de Uses-kolom van de zojuist gemapte component.
Voor elke nog-niet-gemapte **interne** Use: ga direct door met mappen — dat is
onderdeel van het completeren van de oorspronkelijke component, geen aparte pass.

- Agent meldt vooraf: *"Ik map nu X, daarna automatisch ook Y en Z (Uses van X)."*
- Geen aparte permission-vraag per child-component
- Wel bevestiging vragen bij wijzigingen in code/docs (de standaard regel blijft)

**Stop-grens:** externe libraries (MUI, react-modal, framework-componenten) worden
niet gemapt. Ze worden in de spec genoemd onder "Compositie" of "Voorbeeld", maar
krijgen geen eigen spec.

**Voorbeeld:** als `ConfirmationModal` Uses = `Modal, Button, SecondaryButton`, dan
worden alle drie gemapt in dezelfde sessie. `Modal` op zijn beurt gebruikt
`react-modal` extern — daar stopt de keten.

Bij echt ontbrekende code (component bestaat niet, terwijl Figma 'm wel toont):
stop en markeer als `component-missing` drift, vraag wat te doen.

## Wat lees je wanneer

| Taak | Lees eerst |
|---|---|
| Token-waarde controleren of toevoegen | `docs/tokens.md` |
| Component bouwen of gebruiken | `docs/components.md`, relevante `docs/components/<naam>.md` |
| Figma-frame mappen naar code | `docs/components.md`, betrokken specs; refresh `figma-context/<node-id>.json` via MCP wanneer beschikbaar |

## Drift — kort en pragmatisch

Drift wordt gemarkeerd in de spec waar 'ie hoort, niet als apart proces. Drie types:

- **`value-mismatch`** — Code rendered output ≠ Figma. Fix op call-site.
- **`token-mismatch`** — Token-waarde in `theme/tokens.ts` ≠ Figma. Fix in theme.
- **`component-missing`** — Figma-element zonder code-component. Markeer; **agent niet auto-genereren**, ontwikkelaar maakt component.

Format: één regel per drift in de "Drift-aandachtspunten"-sectie van de spec.

```
- <type> — <bestand:regel> <wat verschilt>. Actie: <wat te doen>.
```

### Drift-test

Bij elk kandidaat-drift, één vraag: **"Zou MCP-codegen vanuit deze Figma-node een visueel verkeerd resultaat opleveren?"**

- Ja → drift, in `drifts.md` + spec.
- Nee → geen drift. Hoort thuis in een andere bucket:
  - **`verify-queue.md`** — `[VERIFY]`-items die in een volgende sessie met live MCP geresolved worden (ongeverifieerde masters, ongelokaliseerde overrides, gederiveerde data zonder bron-check)
  - **Tech-debt** — hardcoded-met-correcte-waarde, tokenization-kandidaten, dead code → niet in mapping-docs, hoort in code-review of issue-tracker
  - **Mapping-doc-fix** — `tokens.md` of `components.md` was foutief gedocumenteerd → direct fixen in dat doc, geen drift-rij

Drift gaat over visuele/structurele mapping. NIET over: tekst-content/copy,
hardcoded-met-zelfde-waarde (tech-debt), code-bugs, auto-layout container-properties
zonder token-binding, ongeverifieerde masters (die horen in `verify-queue.md`),
of `figma-master-missing` voor code-only abstracties (administratief, geen drift).

## Selectie van componenten

Bij het mappen van een Figma-frame naar code: **organism eerst, dan molecules, dan atoms.**

- **Atom** — onsplitsbaar (Button, Input, Icon, Badge)
- **Molecule** — samenstelling van atoms met één gedeeld doel
- **Organism** — eigen state, scroll-gedrag, of keyboard-handling

Bij twijfel: kies het lagere niveau.

## Wat je niet doet

- Geen nieuwe componenten genereren zonder bevestiging
- Geen nieuwe tokens toevoegen aan code zonder bevestiging
- Geen aannames over wat de gebruiker bedoelde — vragen
- Geen losse atom-recombinaties als een organism het werk al doet
- Geen folders of bestanden aanmaken zonder eerst te vragen
- **Geen gedragsdocumentatie.** Specs bevatten alléén mapping-data: wat heeft MCP nodig om correct te genereren? Niet "Wanneer gebruiken", "Edge cases", "Wat dit component toevoegt", "Selectie-disambiguatie", hover/focus/active-narratief, a11y-uitleg. Dat leeft in code en is buiten scope. Visuele verwarbaarheid wordt opgelost via Variant-mapping en Master-id, niet via prosa.

## Wat dit niet is

- Geen design-system documentatie-tool. Doel is mapping, niet een complete design-laag bouwen.
- Geen drift-detectie als hoofdfunctie. Drift wordt kort gemarkeerd; mapping is het primaire werk.
- Geen vervanging voor Figma Code Connect. Voor projecten waar Code Connect is opgezet, doet die de mapping automatisch.
- Geen documentatie van gedrag. Hover, focus, motion leven in code.

## Lessons learned

Vastleggen wanneer een regel **niet werkte** (correctie nodig) **én** wanneer een regel **wél werkte** (bewust herhalen).

```
[LESSON — datum] [type: correctie | bevestiging]
Situatie: [wat gebeurde]
Wat werkte (of niet): [observatie]
Voorstel: [regel aanpassen | regel houden]
```

---

[LESSON — 2026-05-05] [bevestiging]
Situatie: Live MCP-test op modal-frame `16599:2645` na strakke drift-test + mapping-only specs.
Wat werkte: Drift-test als filter werkte: 7 kandidaat-issues kwamen binnen, 5 slaagden voor de test als echte drift, 0 ruis. 2 nieuwe Major/Minor Button-drifts ontdekt die zonder MCP-test onzichtbaar waren (padding-x 24 vs 20, radius 40 vs 44). Mapping-only specs gaven scherpe tabel-vergelijking met MCP-output, geen narratief in de weg.
Voorstel: Drift-test + mapping-only specs houden — dit is het juiste regime voor MCP-codegen-kwaliteitsdoel.

[LESSON — 2026-05-05] [correctie]
Situatie: Bij eerste mapping-pass werd de Button-spec geschreven zonder vergelijking tussen code-waardes en Figma-rendered output. Tabellen toonden alleen code-zijde ("Padding (medium) | 16px 24px | theme.spacing.lg + theme.spacing.xl"). Drifts (padding-x 4px afwijking, radius 4px afwijking) werden pas in de re-validatie-pass gevonden.
Wat niet werkte: Spec-tabellen zijn alleen code-documentatie; ze vergelijken niet expliciet met Figma. Drift-detectie hangt af van de re-validatie i.p.v. al bij eerste mapping-pass.
Voorstel: Bij A4 (mapping aan Figma) verplicht maken: voor elk hardcoded-waarde-element in code een directe vergelijking met de Figma-instance-waarde uit MCP-output. Niet alleen "code-waarde + token" noteren — ook expliciet checken of Figma diezelfde waarde toont. Wanneer Figma afwijkt: drift in spec, niet alleen in volgende re-validatie.

## Verwijzingen

- `templates/tokens.md` — leeg tokens-template
- `templates/components.md` — leeg components-index template
- `templates/component-spec.md` — template voor één component-spec
- `templates/claude-md-snippet.md` — markdown om in projectrepo CLAUDE.md te plakken
- `templates/example-confirmation-modal/` — ingevuld voorbeeld
