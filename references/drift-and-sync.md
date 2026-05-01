# Drift en sync-richting

Achtergrond bij hoe drift wordt gemarkeerd en hoe sync-richting wordt bewaakt.

## Drift-typen

Drift wordt gemarkeerd, niet uitvoerig gerapporteerd. Drie typen volstaan voor de
meeste situaties.

### `DRIFT: token-mismatch`

Figma-waarde wijkt af van token-waarde, of Figma gebruikt geen token waar wel een
hoort.

**Voorbeeld:**
```
DRIFT: token-mismatch — Modal box-shadow Figma rgba(135,173,187,0.20) ≠ code rgba(29,38,50,0.20)
```

### `DRIFT: component-missing`

Figma-element heeft geen mapping in `components.md`. Vraag of nieuwe component nodig is,
of dat een bestaande gebruikt kan worden.

**Voorbeeld:**
```
DRIFT: component-missing — Figma frame 'NotificationBanner' heeft geen entry in components.md
```

### `DRIFT: hardcoded-value`

Code gebruikt hardcoded waarde waar token bestaat. Vaak een quick-win voor consistentie.

**Voorbeeld:**
```
DRIFT: hardcoded-value — confirmation-modal.styles.ts lineHeight '16px' kan theme.lineHeights.xs gebruiken
```

## Format

Eén regel per drift, ga door met de hoofdtaak. Geen uitvoerige analyse.

```
DRIFT: [type] — [korte beschrijving]
```

Bij meerdere drift-punten in één component-spec: lijst ze samen onderaan in de
"Drift-aandachtspunten" sectie van die spec.

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

## Wat doen wanneer drift gevonden

1. Markeer in de "Drift-aandachtspunten" sectie van de relevante component-spec
2. Ga door met het hoofdwerk (mappen) — drift is bijproduct, geen blokkade
3. Bij einde van sessie of na een batch werk: vraag de gebruiker of drift-punten
   moeten worden opgepakt

Drift-punten zijn **niet** een verzoek om code te fixen. Ze maken zichtbaar wat
mogelijk gefixt zou moeten worden. De fix is mensenwerk en kan een aparte werkstroom
zijn.
