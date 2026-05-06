---
name: figma-to-code
version: "2.5"
description: >
  Mapt Figma-designs op een bestaande codebase via expliciete documentatie van tokens,
  componenten, en per-component-specs. Gebruik deze skill wanneer de gebruiker zegt
  "documenteer deze component", "map dit Figma-frame", "voeg X toe aan de mapping",
  of "werk de tokens bij". Ook triggeren bij "figma-to-code", "design-to-code", of
  wanneer de projectrepo een figma-to-code-mapping folder heeft met tokens.md en
  components.md.
  Doel: MCP-codegen vanuit Figma matcht ≥90% met de bestaande codebase, doordat
  tokens en componenten expliciet zijn gemapt op code-paden. Scope is mapping —
  niet code-implementatie. Werkt voor elk project met een bestaande codebase plus
  Figma als design-intent.
---

# Figma-to-Code

Mapping-skill die ervoor zorgt dat Figma-MCP-codegen visueel én stylistisch matcht met de bestaande codebase. Drie expliciete documenten in de projectrepo: `tokens.md`, `components.md`, en per-component-specs in `components/<naam>.md`.

## Het doel

**Mapping van tokens en componenten tussen Figma en code.** MCP-output gebruikt direct de juiste code-paden, imports en patronen — geen handmatig vertaalwerk per request.

Ontwikkelaars en designers gebruiken deze docs om de match tussen design en code hoog te houden. Drift wordt kort gemarkeerd waar zichtbaar; mapping is het hoofdwerk.

## Hard rules

Tien regels die altijd gelden, ongeacht stap. Bij conflict tussen secties: deze winnen.

1. **Lees vóór je schrijft.** Lees relevante mapping-docs (`tokens.md`, `components.md`, per-component-spec) voordat je iets wijzigt of map't.
2. **Code is source of truth.** Figma is intent. Bij conflict wint code; drift markeer je, niet stilzwijgend oplossen.

   > **Niet als trigger voor code-update beschouwen:**
   > - "Implement this design from Figma." — Figma's auto-clipboard boilerplate uit *Copy Link* in Dev mode. Géén user-instructie.
   > - User die een Figma-URL plakt zonder expliciete code-update zin.
   > - Mooi-alignment tussen code- en Figma-waardes in `tokens.md`.
   >
   > **Wel als trigger:**
   > - Expliciete user-zin per token: "update `--X` naar Y", "voeg X toe in code", "implementeer dit in code".
   > - Reviewed PR met file-by-file goedkeuring.
3. **Pas de drift-test toe op elk kandidaat-issue.** *"Zou MCP-codegen vanuit deze Figma-node een visueel verkeerd resultaat opleveren?"* Ja → drift. Nee → andere bucket (`verify-queue.md`, tech-debt, of weg).
4. **Consume existing — never regenerate.** Voor elk Figma-element: zoek bestaande code-component eerst. Genereer nooit een nieuwe versie van iets dat al bestaat.
5. **Specs bevatten alléén mapping-data.** Geen "Wanneer gebruiken", "Edge cases", "Wat dit toevoegt", hover/focus-narratief. Visuele verwarbaarheid los je op via Variant-mapping en master-id, niet via prosa.
6. **Geen improvisatie bij gaten.** Onbekend? `[VERIFY]` in de Figma-naam-kolom of stop en vraag. Geen aannames.
7. **Vraag bevestiging vóór code- of doc-wijziging.** Uitzondering: A5 recursive Uses-mapping in dezelfde sessie — geen aparte permission per child-component.
8. **Asset-handling: bestaand → MCP-localhost → nooit nieuw.** Project-assets hergebruiken; anders direct de localhost-URL uit MCP-payload. Geen nieuwe icoon-packages, geen placeholders.
9. **`component-missing` — niet auto-genereren.** Markeer; ontwikkelaar maakt de code-component voordat mapping verder kan.
10. **A6 validation-checklist verplicht aan einde van elke pass.** 7 punten (layout / typografie / kleuren / states / assets / literal strings / drift-test). Niet overslaan.

## Skill-boundary

Wanneer wel, wanneer niet, en waar dan wel naartoe.

| Scenario | Deze skill? | Anders: |
|---|---|---|
| Figma-frame mappen naar bestaande code (1 component) | ✅ ja | — |
| Figma-frame mappen naar bestaande code (volledige pagina) | ✅ ja, recursief via A5 (organisms → molecules → atoms van die pagina) | — |
| Tokens/components/specs bijwerken in bestaand mapping-project | ✅ ja | — |
| Drift detecteren tussen Figma en bestaande code | ✅ ja, als bijproduct van mapping | — |
| Figma-frame **implementeren als werkende code** | ❌ nee | Buiten scope. Deze skill bouwt alléén de mapping; code-generatie is downstream-werk en hoort in een aparte skill |
| Hele pagina **vanuit een tekstbeschrijving** bouwen (geen Figma-input) | ❌ nee | `figma-generate-design` of `frontend-design` (greenfield) |
| Figma-bestand **schrijven** (nodes maken, variabelen aanmaken) | ❌ nee | `figma-use` |
| Code Connect-mappings (`.figma.ts`) maken | ❌ nee | `figma-code-connect` |
| Een design-system bouwen in Figma vanuit code | ❌ nee | `figma-generate-library` |
| AI-rules schrijven voor een project (CLAUDE.md / AGENTS.md) | ❌ nee | `figma-create-design-system-rules` |

**Volledige pagina nuance.** Onze skill werkt voor pagina-Figma-frames net zo goed als voor losse componenten — input is in beide gevallen een Figma-node. Het verschil met `figma-generate-design` zit in de input-vorm: wij hebben Figma-pixels nodig, zij accepteren tekst-briefings. Voor een pagina draaien we A1–A6 recursief, één organism per keer, totdat alle children gemapt zijn.

**Wat dit niet is:**
- Geen design-system-documentatie-tool — doel is mapping, geen complete design-laag bouwen.
- Geen drift-detectie als hoofdfunctie — drift wordt kort gemarkeerd; mapping is het primaire werk.
- Geen vervanging voor Figma Code Connect — waar Code Connect bestaat, doet die de mapping automatisch.
- Geen documentatie van gedrag — hover, focus, motion, keyboard-handling leven in code.

## Slash-commando's

- `/figma-to-code setup` — vraag bevestiging, dan `docs/`-structuur aanmaken in projectrepo
- `/figma-to-code map <component>` — start mapping van die component (volledige A1-A6)
- `/figma-to-code init-claude-md` — toon markdown-blok om in projectrepo CLAUDE.md te plakken

## Bron-verdeling

**Figma is intentie.** Wat moet er komen, welke schermen, welke nieuwe componenten.

**Code is waarheid.** Welke tokens en componenten bestaan, met welke waardes.

Bij conflict wint code. Drift markeer je expliciet, niet stilzwijgend oplossen.

## Bron-mechanisme

### Cache + hash-check

**Cache is de werkende laag.** Map vanuit een lokale cache van Figma-node-data. Conventie: `figma-context/<node-id>.json`, één bestand per node. Mapping leest uit de cache, niet rechtstreeks uit Figma.

**Cache-format met spec-link.** Elk cache-bestand bevat metadata over de gekoppelde code-component en de laatste sync-status:

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

**Hash-check bij elke pass.** Hash de huidige code-bestanden, vergelijk met `spec_synced_with_files_hash`. Bij verschil: spec is out-of-sync sinds code-wijziging. Voorkomt dat je werkt met verouderde mappings.

### MCP-tools en fallbacks

**MCP is het verversmechanisme.** Bij elke mapping-pass: live MCP-fetch, vergelijk met cache, schrijf nieuwste versie weg, map vanuit de bijgewerkte cache.

**Selection-based MCP zonder node-id.** Wanneer Figma desktop open is met een node geselecteerd én de MCP-tool ondersteunt selectie-fallback (`mcp__Figma__get_design_context` zonder `nodeId`): gebruik dat. Wel: leg de uiteindelijk gefetchte node-id altijd vast in spec en cache, zodat de mapping reproduceerbaar is. Geen anonieme "current selection"-mappings.

**Mapping zonder actieve MCP** kan, mits cache aanwezig is — noteer dan `cache_verified_via_mcp: false` in het cache-bestand met reden.

**Master-verificatie via instance-id-format.** Wanneer een master-page niet direct bereikbaar is via MCP (master ligt op andere page), maar instances ervan wél bereikbaar zijn via een ander frame: het instance-id-format `I<frame-id>;<master-id>` is voldoende bewijs voor master-id-verificatie. Master-cache is niet vereist — instance-rendering binnen een geverifieerd frame levert alle mapping-data die voor MCP-codegen nodig is. Documenteer in cache-bestand: `master_verified_via: "instance-id-format"`.

### Asset-handling (SVG, images, icons)

MCP-output bevat regelmatig asset-URLs in de vorm `http://localhost:3845/assets/<hash>.svg` of `<hash>.png`. Drie regels:

1. **Bestaande asset zoeken eerst.** Als de codebase al een asset heeft die deze Figma-asset representeert (bv. `images/icons/ui/close.svg?react` voor close-icoon), gebruik die. Map in de spec onder de mapping-tabel: `Icon-source | images/icons/ui/close.svg?react | lokale SVG-import`.
2. **Geen nieuwe icoonpackages installeren.** Geen `npm install lucide-react`, geen `@mui/icons-material`-import "voor de zekerheid". Alle assets komen óf uit bestaande project-assets, óf direct uit de Figma MCP-payload (localhost-URL).
3. **Geen placeholders.** Wanneer MCP een `localhost`-URL teruggeeft: gebruik die direct, of download het asset eenmalig naar de project-conventie-locatie en map daarheen. Nooit een placeholder of TODO-comment achterlaten.

Wanneer een Figma-asset niet in code bestaat én niet uit MCP komt: stop, vraag de gebruiker. Niet improviseren met een lookalike.

## De documenten

| Document | Doel |
|---|---|
| `docs/tokens.md` | Driekoppige mapping (Figma-naam → code-pad → waarde) |
| `docs/components.md` | Index met Uses-kolom en atomic-design classificatie |
| `docs/components/<naam>.md` | Per component: spec, props, states, mapping naar Figma |
| `docs/drifts.md` | Centrale drift-aggregatie (alleen drift-test-passers) |
| `docs/verify-queue.md` | `[VERIFY]`-items voor volgende live-MCP-sessie |

Templates staan in `templates/`. Bij `setup`-commando worden ze gekopieerd naar de projectrepo.

## Wat lees je wanneer

| Taak | Lees eerst |
|---|---|
| Token-waarde controleren of toevoegen | `tokens.md` |
| Component bouwen of gebruiken | `components.md`, relevante `components/<naam>.md` |
| Figma-frame mappen naar code | `components.md`, betrokken specs; refresh `figma-context/<node-id>.json` via MCP wanneer beschikbaar |
| Drift-besluit nemen | `drifts.md` (bestaande), spec van betrokken component, `verify-queue.md` |
| Onbekende Figma-naam tegenkomen | `verify-queue.md` (mogelijk al bekend), anders nieuw `[VERIFY]`-item toevoegen |

## Selectie van componenten

Twee regels die samen werken (Hard rule #4 + atomic-volgorde):

**1. Consume existing — never regenerate.** Voor elk Figma-element: zoek eerst of er een matchend code-component bestaat (via `components.md`, `src/components/`-scan, of Code Connect). Als ja: import en gebruik. Genereer nooit een nieuwe versie — een afwijkend gebruik is óf een prop-keuze, óf drift, óf legitieme reden voor uitbreiding van het bestaande component.

**2. Organism eerst, dan molecules, dan atoms.** Wanneer er meerdere geldige code-componenten kunnen matchen: kies het hoogste atomic-level dat past. Combineer geen losse atoms als er al een molecule of organism het werk doet.

- **Atom** — onsplitsbaar (Button, Input, Icon, Badge)
- **Molecule** — samenstelling van atoms met één gedeeld doel
- **Organism** — eigen state, scroll-gedrag, of keyboard-handling

Bij twijfel: kies het lagere niveau. Bij volledige afwezigheid van een matchend component → `component-missing` drift, niet auto-genereren (Hard rule #9).

## Drift — kort en pragmatisch

Drift markeer je in de spec waar 'ie hoort, niet als apart proces. Drie types:

- **`value-mismatch`** — Code rendered output ≠ Figma. Fix op call-site.
- **`token-mismatch`** — Token-waarde in `theme/tokens.ts` ≠ Figma. Fix in theme.
- **`component-missing`** — Figma-element zonder code-component. Markeer; agent niet auto-genereren, ontwikkelaar maakt component.

Format: één regel per drift in de "Drift-aandachtspunten"-sectie van de spec.

```
- <type> [Severity][Owner] — <bestand:regel> <wat verschilt>. Actie: <wat te doen>.
```

### Drift-test (gebruik bij elk kandidaat-drift)

Eén vraag, altijd: **"Zou MCP-codegen vanuit deze Figma-node een visueel verkeerd resultaat opleveren?"**

- **Ja** → drift, in `drifts.md` + spec.
- **Nee** → andere bucket:
  - `verify-queue.md` — `[VERIFY]`-items voor volgende sessie met live MCP (ongeverifieerde masters, ongelokaliseerde overrides, gederiveerde data zonder bron-check).
  - **Tech-debt** — hardcoded-met-correcte-waarde, tokenization-kandidaten, dead code → niet in mapping-docs, hoort in code-review of issue-tracker.
  - **Mapping-doc-fix** — `tokens.md` of `components.md` was foutief gedocumenteerd → fix direct in dat doc, geen drift-rij.

Drift gaat over visuele/structurele mapping. NIET over: tekst-content/copy, hardcoded-met-zelfde-waarde (tech-debt), code-bugs, auto-layout container-properties zonder token-binding, ongeverifieerde masters (die horen in `verify-queue.md`), of `figma-master-missing` voor code-only abstracties (administratief, geen drift).

## Werkwijze A1-A6

### A1. Inventariseer

Scan de bestaande codebase. Identificeer:
- Waar tokens leven (CSS variables, theme-object, Tailwind config, mix)
- Welke component-folders er zijn (`src/components/ui/`, etc.)
- Welk format styles gebruikt (CSS modules, Emotion, styled-components, etc.)

Geef de gebruiker een korte samenvatting voor je doorgaat.

### A2. Vul tokens.md (incrementeel)

Documenteer alleen tokens die het eerste component raakt. Volgende componenten breiden de tabellen uit.

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

Per element: haal node-data op via cache (refresh via MCP), lees de code, en stel een mapping voor (Figma-property → code-token of code-pad). Gebruiker bevestigt. Bij twijfel: `[VERIFY]` in de Figma-naam-kolom, niet improviseren.

#### A4a. MCP-fetch volgorde bij grote/complexe nodes

1. `get_design_context(nodeId)` — directe haal.
2. **Bij timeout of "te complex"-respons: payload-reductie eerst, vóór splitsen.** MCP-servers ondersteunen verschillende parameters — gebruik wat jouw server heeft:
   - `excludeScreenshot: true` — onderdrukt de screenshot-render, vaak de duurste stap (beschikbaar op fileKey-based MCP, niet op alle desktop-active MCPs)
   - `forceCode: true` — dwingt code-output ook bij truncation-risico (beschikbaar op beide gangbare Figma-MCPs)

   Een retry met payload-reductie is goedkoper dan direct splitsen via metadata.
3. **Bij blijvende truncatie of timeout:** `get_metadata(nodeId)` voor de child-tree.
4. Identificeer de relevante child-nodes uit de metadata-XML.
5. Loop door en `get_design_context(<childId>)` per relevante child (eventueel met payload-reductie); assembleer het beeld.

Niet improviseren als de eerste fetch onvolledig is — altijd via payload-reductie of metadata splitsen voordat je verder map't.

#### A4b. Verplichte vergelijking per hardcoded waarde

Voor elke hardcoded waarde in code (padding, border-radius, height, etc.) direct vergelijken met de Figma-instance-waarde uit MCP-output. Niet alleen "code-waarde + token" noteren — pas expliciet de drift-test toe (Hard rule #3): matcht de Figma-rendered output? Bij verschil: drift in spec + `drifts.md`, niet pas in re-validatie. **Drift-detectie hoort bij A4, niet later.**

#### A4c. Literal strings zijn ook mapping

Component-specifieke strings die in code geëmit worden — `aria-label`, `alt`, `placeholder`, `title`-attributen — zijn mapping-data, geen gedrag. Map ze net als tokens: code-waarde + Figma-bron. Voorkomt dat MCP-codegen een generieke `aria-label="Close"` produceert in plaats van het bestaande `"Sluit venster"`. Niet alle a11y-aspecten zijn mapping (focus-traps, keyboard-navigatie zijn gedrag) — wel deze literale strings.

### A5. Recursief Uses afmaken (zonder aparte permission-vraag)

Na elke component-mapping: scan de Uses-kolom van de zojuist gemapte component. Voor elke nog-niet-gemapte **interne** Use: ga direct door met mappen — onderdeel van het completeren van de oorspronkelijke component, geen aparte pass.

- Agent meldt vooraf: *"Ik map nu X, daarna automatisch ook Y en Z (Uses van X)."*
- Geen aparte permission-vraag per child-component (uitzondering op Hard rule #7)
- Wel bevestiging vragen bij wijzigingen in code/docs (Hard rule #7 blijft)

**Stop-grens:** externe libraries (MUI, react-modal, framework-componenten) worden niet gemapt. Ze worden in de spec genoemd onder "Compositie" of "Voorbeeld", maar krijgen geen eigen spec.

**Voorbeeld:** als `ConfirmationModal` Uses = `Modal, Button, SecondaryButton`, worden alle drie gemapt in dezelfde sessie. `Modal` op zijn beurt gebruikt `react-modal` extern — daar stopt de keten.

Bij echt ontbrekende code (component bestaat niet, terwijl Figma 'm wel toont): stop en markeer als `component-missing` drift, vraag wat te doen.

### A6. Validation-checklist (afsluiting per mapping-pass)

Aan het einde van elke component-mapping (vóór commit/sync) loop je deze 7 checks expliciet langs. Drift-test is filter (wat ga ik markeren?); deze checklist is positief (heb ik niets stilzwijgend laten lopen?).

| # | Check | Waar gevalideerd |
|---|---|---|
| 1 | **Layout** — sizing, spacing, alignment matchen MCP-output (binnen scope-regels) | Mapping-tabellen + drift-aandachtspunten |
| 2 | **Typografie** — font-family, size, weight, line-height matchen Figma-style | Mapping-tabel onder "Tekst" |
| 3 | **Kleuren** — exact match op Figma-variabele (Yellow/Y100, Blue/B30, etc.) of `[VERIFY]` | Mapping-tabel onder "Container/Kleur" |
| 4 | **States** — variants en states (hover/focus/active/disabled) gemapt waar Figma die toont | Variant-mapping subsectie |
| 5 | **Assets** — SVG/icon/image-bronnen verwijzen naar bestaande project-assets of MCP-localhost-URL — geen nieuwe imports, geen placeholders | Mapping-tabel "Icon-source / Asset" |
| 6 | **Literal strings** — `aria-label`, `alt`, `placeholder`, `title`, hardcoded labels in code zijn gemapt (code-waarde + bron) | Mapping-tabel "Tekst" of aparte rij "Aria-label" |
| 7 | **Drift-test gepasseerd** — kandidaat-issues geclassificeerd: drift, verify-queue, of weg | `drifts.md` + `verify-queue.md` |

Vink in de spec onder "Drift-aandachtspunten" af: *"Spec laatst gevalideerd: [datum] (A6 doorlopen)."*

## Lessons learned

Vastleggen wanneer een regel **niet werkte** (correctie nodig) **én** wanneer een regel **wél werkte** (bewust herhalen). Strict format, max 5 regels per entry. Nieuwe entries onderaan.

```
[LESSON — YYYY-MM-DD] [type: correctie | bevestiging]
Situatie: <wat gebeurde, 1 regel>
Wat werkte (of niet): <observatie, 1-2 regels>
Voorstel: <regel aanpassen of houden, 1 regel>
```

---

[LESSON — 2026-05-05] [bevestiging]
Situatie: Live MCP-test op modal-frame `16599:2645` na strakke drift-test + mapping-only specs.
Wat werkte: Drift-test als filter werkte: 7 kandidaten binnen, 5 echte drifts, 0 ruis. 2 nieuwe Button-drifts gevonden (padding-x 24 vs 20, radius 40 vs 44).
Voorstel: Drift-test + mapping-only specs houden — juiste regime voor MCP-codegen-kwaliteit.

[LESSON — 2026-05-05] [correctie]
Situatie: Eerste Button-mapping noteerde alleen code-waardes ("16px 24px | theme.spacing.lg + xl") zonder Figma-vergelijking. Drifts pas in re-validatie gevonden.
Wat niet werkte: Spec-tabellen waren code-documentatie, geen mapping-vergelijking. Drift-detectie hing aan re-validatie i.p.v. eerste pass.
Voorstel: A4 verplicht maken: per hardcoded waarde direct vergelijken met Figma-instance. Drift bij eerste pass markeren, niet later. (Doorgevoerd in A4b.)

[LESSON — 2026-05-05] [correctie]
Situatie: Agent interpreteerde Figma's auto-paste "Implement this design from Figma." (uit *Copy Link* Dev mode) als user-instructie en deed token + button.tsx changes, gevolgd door revert.
Wat niet werkte: Boilerplate-tekst werd verward met expliciete dev-task. Hard Rule #2 onbedoeld weggeredeneerd zonder dat user iets vroeg.
Voorstel: Hard Rule #2 expliciet uitsplitsen wat wel/niet als code-update-trigger geldt. (Doorgevoerd in v2.4.)

[LESSON — 2026-05-05] [bevestiging]
Situatie: get_design_context op shadcn-kit Button size-symbols (1463:5702/5739/5737) gaf MCP-timeout. Eerste pass strandde drie items in verify-queue.
Wat werkte: Retry met payload-reductie-parameter (`excludeScreenshot: true` op fileKey-based MCP) slaagde direct op alle 3 nodes. Goedkoper dan metadata-split.
Voorstel: A4a uitbreiden — payload-reductie als eerste fallback voor timeouts, vóór metadata-split. (Doorgevoerd in v2.5.)

## Verwijzingen

- `templates/tokens.md` — leeg tokens-template
- `templates/components.md` — leeg components-index template
- `templates/component-spec.md` — template voor één component-spec
- `templates/drifts.md` — leeg drift-aggregator template
- `templates/verify-queue.md` — leeg `[VERIFY]`-queue template
- `templates/claude-md-snippet.md` — markdown om in projectrepo CLAUDE.md te plakken
- `templates/example-confirmation-modal/` — ingevuld voorbeeld
