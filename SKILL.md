---
name: figma-to-code
version: "1.0"
description: >
  Mapt Figma-designs op een bestaande codebase via expliciete documentatie van tokens,
  componenten, en per-component-specs. Gebruik deze skill wanneer de gebruiker zegt
  "documenteer deze component", "map dit Figma-frame", "voeg X toe aan de codebase",
  "werk de tokens bij", of een Figma-link deelt voor implementatie. Ook triggeren
  bij "figma-to-code", "design-to-code", "drift-check", of wanneer de gebruiker werkt
  aan een Blis-project met `docs/tokens.md` en `docs/components.md` aanwezig.
  Het doel is dat code visueel 1-op-1 matcht met Figma — geen drift, geen improvisatie,
  geen handmatig vergelijken. Werkt voor elk project met een bestaande codebase plus
  Figma als design-intent.
---

# Figma-to-Code

Mapping-skill voor projecten met een bestaande codebase. Zorgt dat Figma-designs en code
visueel matchen via drie expliciete documenten in de projectrepo: `tokens.md`,
`components.md`, en per-component-specs in `components/<naam>.md`.

## Het doel

**Mapping en consistentie tussen Figma en code.** Twee soorten regels werken samen:

1. **Tokens** — voorkomen dat hex-waardes worden geïmproviseerd
2. **Componenten** — voorkomen dat samenstellingen worden geïmproviseerd

**De agent voert de mapping uit** op basis van Figma-data (via cache) en code-lezing.
De gebruiker bekrachtigt; de agent improviseert niet en delegeert ook niet alles aan
de mens.

Gedrag (hover, focus, motion) leeft in code en wordt niet apart gedocumenteerd.
De code is de waarheid.

Drift-detectie is een **bijproduct**, geen doel. Wanneer drift voorkomt: markeer kort,
ga door met mappen.

## Bron-verdeling

**Figma is intentie.** Wat moet er komen, welke schermen, welke nieuwe componenten.

**Code is waarheid.** Welke tokens en componenten bestaan, met welke waardes.

Bij conflict wint code. Drift wordt gemarkeerd, niet stilzwijgend opgelost.

## Bron-mechanisme

**Cache is de werkende laag.** De agent map't tegen een lokale cache van
Figma-node-data. Conventie: `figma-context/<node-id>.json`, één bestand per node.
De skill schrijft de locatie niet voor; het project bepaalt. Mapping leest dus uit
de cache, niet rechtstreeks uit Figma.

**MCP is het verversmechanisme.** Figma-MCP houdt de cache up-to-date. Bij elke
mapping-pass: agent doet een live MCP-fetch, vergelijkt met de cache, schrijft de
nieuwste versie weg, en map't vervolgens vanuit de bijgewerkte cache. Cache-management
is process-werk, geen drift in component-specs.

**Mapping zonder actieve MCP.** Mapping kan doorgaan wanneer MCP tijdelijk niet
beschikbaar is, mits de cache aanwezig is. Noteer dan in de mapping-pass dat de cache
niet via MCP is geverifieerd. Cache-only werken is een tijdelijke modus, niet de
standaard.

## De documenten in de projectrepo

| Document | Doel |
|---|---|
| `docs/tokens.md` | Driekoppige mapping (Figma-naam → code-pad → waarde) |
| `docs/components.md` | Index met Uses-kolom en atomic-design classificatie |
| `docs/components/<naam>.md` | Per component: spec, props, states, mapping naar Figma |

Templates voor deze documenten staan in `templates/` van deze skill. Bij `setup`-commando
worden ze gekopieerd naar de projectrepo.

## De werkwijze-regel

Voor elke taak, zonder uitzondering:

1. **Lezen voor schrijven.** Lees eerst de relevante documenten. Geen code, geen
   wijziging zonder eerst te lezen.

2. **Documenteer wat is, niet wat zou moeten zijn.** Code is source of truth. Als de
   codebase geen primitive/semantic split heeft, forceer er geen op. Volg de codebase.

3. **Geen improvisatie bij gaten.** Als iets niet in de documenten staat, stop. Vraag
   de gebruiker. Maak geen aannames over kleur, spacing, motion, naming, of welke
   component te gebruiken.

4. **Bevestiging voor doorvoeren.** Wanneer je een wijziging voorstelt — in code of
   in een document — vraag bevestiging voordat je doorvoert.

5. **Bij selectie van componenten: organisms eerst.** Check eerst of een organism past,
   dan molecules, dan atoms. Combineer geen atoms als er al een hogere-orde component
   bestaat.

6. **Altijd vragen voor folders aanmaken.** Bij `setup`-commando: vraag eerst of je de
   `docs/`-structuur mag aanmaken. Pas na bevestiging doorvoeren.

## Slash-commando's

- `/figma-to-code` — activeer werkwijze, wacht op vervolgvraag
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

### A2. Vul tokens.md (incrementeel, niet de hele laag)

Documenteer alleen tokens die het eerste component raakt. Volgende componenten breiden
de tabellen uit.

Per token een rij met:
- Code-pad (hoe je het in components gebruikt)
- Figma-naam (zoals het in Figma als variabele bestaat) of `[VERIFY]` indien onbevestigd
- Waarde
- Gebruik (korte uitleg)

**Niet forceren wat er niet is.** Als de codebase geen semantic-laag heeft, schrijf er geen.

### A3. Documenteer eerste component

Kies samen met de gebruiker één representatieve component. Voor dat component:

1. Lees de implementatie (component-file, styles-file, types-file)
2. Lees de wrapper-laag indien aanwezig
3. Vul `docs/components/<naam>.md` op basis van `templates/component-spec.md`
4. Voeg toe aan `components.md` met Uses-kolom

### A4. Mapping aan Figma

Per element in de component-spec: agent haalt node-data op via MCP, leest de code, en
stelt een mapping voor (Figma-property → code-token of code-pad). Gebruiker bevestigt
of corrigeert. Code's semantische rollen leven niet in Figma — daarom is bevestiging
nodig, geen blanco automation. Bij twijfel: `[VERIFY]` in de Figma-naam-kolom, niet
improviseren.

**Mapping is doorlopend werk, niet eenmalig:**
- Bij elk nieuw component groeit `tokens.md` met nieuwe rijen
- Bij elke nieuwe Figma-variabele die in scope komt: voeg toe of markeer drift
- Bij elke wijziging in code-tokens of Figma-variabelen: tabel bijwerken

Onzekere mappings krijgen `[VERIFY]` in de Figma-naam-kolom. Bij volgend werk: oplossen.

### A5. Volgende componenten

Bij elk volgend component: dezelfde stappen, maar nu kunnen `tokens.md` en
`components.md` hergebruikt worden. Bij ontbrekende tokens of componenten: stop en
vraag.

## Wat lees je wanneer

| Taak | Lees eerst |
|---|---|
| Token-waarde controleren of toevoegen | `docs/tokens.md` |
| Component bouwen of gebruiken | `docs/components.md`, relevante `docs/components/<naam>.md` |
| Figma-frame mappen naar code | `docs/components.md`, betrokken specs; refresh `figma-context/<node-id>.json` via MCP wanneer beschikbaar, vervolgens map vanuit de cache |

## Sync-richting — wat checken bij wijzigingen

Mapping is doorlopend werk. Bij elke wijziging: check welke andere documenten of
bestanden geraakt worden, voer impact-check uit voor je doorvoert.

| Wijziging in | Check ook |
|---|---|
| `tokens.md` (waarde of naam) | Alle `components/<naam>.md` die het token noemen |
| `components.md` (Uses-kolom) | Component-spec van de wrapper én van de gebruikte componenten |
| `components/<naam>.md` (mapping) | Of de gemapte tokens nog kloppen in `tokens.md` |
| Code (`*.styles.ts` of `*.tsx`) | Bijbehorende `components/<naam>.md` — drift mogelijk |
| Figma-frame | `components/<naam>.md` met die node-id, plus Figma-naam-kolom in `tokens.md` |

Voor elke wijziging: impact tonen, bevestiging vragen, daarna doorvoeren.

## Drift — alleen mapping-relevant, met severity

Drift in mapping-docs is **uitsluitend** wat de mapping-uitkomst (Figma ↔ code visuele match) verbetert. Code-hygiene zonder visueel effect (zoals hardcoded waarde die matcht met een bestaande token), generieke bugs, en design-system-feedback over Figma vallen er buiten.

Drie types:

- **`value-mismatch`** — Code gerenderde output ≠ Figma. Fix op call-site (hardcoded waarde, of verkeerd token gebruikt). Affects alleen die ene component.
- **`token-mismatch`** — Token-waarde in `theme/tokens.ts` ≠ Figma. Fix in theme. Affects alle consumers van dat token.
- **`component-missing`** — Figma-element heeft geen code-component. **Belangrijk: niet auto-genereren.** Markeer alleen; ontwikkelaar maakt de code-component voordat mapping mogelijk is. Altijd Major.

### Format per drift

```
- **<type> [<Severity>][<Audience>]** — <bestand:regel> <wat verschilt> (Δ <delta>). Actie: <wat te doen>.
```

Audience: `[DEV]` standaard. Gebruik `[DEV+DESIGNER]` als de keuze ook designer-input vereist (bv. "code naar Figma of Figma naar code?").

### Severity per dimensie

Severity-bepaling volgt vaste tolerantie-regels:

**Color**
| Afwijking | Severity |
|---|---|
| Verkeerde kleur (token of hex) | Major |
| Kleur niet in design system | Critical |
| Opacity Δ >10% | Major |
| Opacity Δ ≤10% | Minor |

**Spacing (padding, margin, gap)**
| Afwijking | Severity |
|---|---|
| Δ >8px | Major |
| Δ 3–8px | Minor |
| Δ ≤2px | Acceptabel (geen drift) |
| Ontbrekende spacing (0 vs >0) | Major |

**Typography**
| Afwijking | Severity |
|---|---|
| Verkeerde font-family | Critical |
| Verkeerd font-weight | Major |
| Font-size Δ >2px | Major |
| Font-size Δ ≤2px | Minor |
| Line-height Δ >2px | Major |
| Line-height Δ ≤2px | Minor |
| Letter-spacing | Minor |
| Text-color | Major |

**Border radius**
| Afwijking | Severity |
|---|---|
| Δ >4px | Major |
| Δ ≤4px | Minor |
| Pill (999px) vs vaste radius | Major |

**Shadow**
| Afwijking | Severity |
|---|---|
| Shadow ontbreekt of toegevoegd | Major |
| Blur/spread Δ >4px | Minor |
| Kleur-verschil | Minor |
| Offset Δ >2px | Minor |

**Component / Layout**
| Afwijking | Severity |
|---|---|
| Verkeerd component | Critical |
| Ontbrekend component | Critical (= `component-missing`) |
| Verkeerde variant/state | Major |
| Verkeerde flex-direction / uitlijning | Major |
| Verkeerde element-volgorde | Major |
| Extra element niet in design | Minor |

**Iconen**
| Afwijking | Severity |
|---|---|
| Verkeerd icoon | Major |
| Verkeerde grootte Δ >4px | Major |
| Verkeerde grootte Δ ≤4px | Minor |
| Verkeerde kleur | Major |

### Wat NIET als drift markeren

Drifts gaan over **visuele en structurele mapping** (tokens, kleuren, spacing, sizes, shadows, components, layout). NIET over:

- **Tekst-content / copy** (button-labels, dialog-strings, error-messages, placeholders, headings). Dat is content-laag, een andere abstractie. Hardcoded `"Annuleer"` vs Figma `"Annuleren"` → geen drift, hooguit een opmerking voor i18n/copy-team.
- **Hardcoded waarde matcht token-waarde** (bv. `lineHeight: '16px'` waar `theme.lineHeights.xs` ook 16px is). Visueel identiek, mapping is correct. Of code de token gebruikt of niet is tech-debt, geen mapping-werk.
- **Code-bugs zonder visueel effect** (i18n, accessibility, performance) → issue-tracker
- **Figma heeft geen master, code heeft wel een component** → mapping werkt prima frame-to-component, geen actie
- **Hardcoded waardes zonder Figma-equivalent** (bv. loading-overlay rgba zonder design-token) → niet drift, hooguit code-hygiene
- **Default-prop waardes die in werkelijke usage altijd worden overschreven** → wel checken: als MCP-codegen de default zou gebruiken, klopt die dan? Zo nee, drift.

### Aggregator

Per project leeft een `drifts.md` in de mapping-folder met alle open drifts uit alle component-specs, gefilterd per audience. Ontwikkelaar opent dat bestand en weet wat te doen — niet 10 specs scannen.

## Selectie van componenten

Bij het mappen van een Figma-frame naar code: **check eerst of een organism past, dan
molecules, dan atoms.**

- **Atom** als de component onsplitsbaar is — Button, Input, Icon, Badge
- **Molecule** als de component een samenstelling is van atoms met één gedeeld doel
- **Organism** als de component eigen state, scroll-gedrag, of keyboard-handling heeft

Bij twijfel: kies het lagere niveau.

## Wat je niet doet

- Geen hardcoded hex-waardes, pixel-waardes, of timing-waardes in code waar tokens bestaan
- Geen nieuwe componenten zonder bevestiging
- Geen nieuwe tokens zonder bevestiging
- Geen aannames over wat de gebruiker bedoelde — vragen
- Geen eigen interpretatie van "subtiel", "modern", "clean" — vraag concrete waardes
- Geen losse atom-recombinaties als een organism het werk al doet
- Geen folders of bestanden aanmaken zonder eerst te vragen

## Wat dit niet is

- Geen design-system documentatie-tool. Doel is mapping, niet een complete design-laag bouwen.
- Geen drift-detectie als hoofdfunctie. Drift wordt kort gemarkeerd; mapping is het primaire werk.
- Geen vervanging voor Storybook of vergelijkbare component-libraries. Aanvulling, niet alternatief.
- Geen vervanging voor Figma Code Connect. Voor projecten waar Code Connect is opgezet, doet die de mapping automatisch. Deze skill is voor projecten zonder Code Connect.
- Geen documentatie van gedrag. Hover, focus, motion leven in code. Deze workflow mapt — de code beschrijft het gedrag.

## Lessons learned

Wanneer deze werkwijze in praktijk niet werkt of incompleet blijkt: voeg hieronder een lesson toe. De gebruiker beslist of de regel wordt aangepast.

```
[LESSON LEARNED — datum]
Situatie: [wat gebeurde]
Probleem: [waarom werkte het niet]
Voorstel: [hoe regel aan te passen]
```

```
[LESSON LEARNED — 2026-05-01]
Situatie: components.md template had één Figma-cel per component-rij — werkte voor code→Figma navigatie maar miste de reverse direction (Figma→code lookup).
Probleem: bij mapping-werk komt regelmatig de vraag "ik zie Figma-node X — welke component is dat?". Met alleen de forward-tabellen moet je alles scannen. Plus: één component heeft vaak meerdere Figma-refs (master + instances + frames waar gebruikt) die niet in één cel passen.
Voorstel: components.md template uitgebreid met een `## Figma-node index` sectie — 4 sub-tabellen voor masters / frames-mapped (figma-master-missing) / instances-pending-verification / pages-mapped. Doorgevoerd in `templates/components.md`.
```

## Verwijzingen

- `templates/tokens.md` — leeg template voor nieuwe projecten
- `templates/components.md` — leeg index-template (incl. Figma-node index voor reverse-lookup)
- `templates/component-spec.md` — template voor één component-spec
- `templates/drifts.md` — leeg drift-aggregator-template (audience-gefilterd)
- `templates/claude-md-snippet.md` — markdown om in projectrepo CLAUDE.md te plakken
- `templates/example-confirmation-modal/` — ingevuld voorbeeld (Blis ConfirmationModal)
- `references/drift-and-sync.md` — drift-typen + sync-richting uitgebreid
