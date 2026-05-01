# Figma-to-Code skill

Mapping-skill voor projecten met een bestaande codebase. Zorgt dat Figma-designs en
code visueel matchen via expliciete documentatie van tokens, componenten, en
per-component-specs.

## Wat het is

Een Claude Code skill die de werkwijze "figma-naar-code mapping" automatiseert. De
skill helpt:

- Drie kerndocumenten in een projectrepo aanmaken (`docs/tokens.md`, `docs/components.md`, `docs/components/`)
- Tokens incrementeel mappen tussen Figma-namen en code-paths
- Componenten classificeren via atomic design met Uses-relaties
- Drift markeren tussen Figma en code zonder dat het hoofdwerk wordt — uitsluitend visuele/structurele mapping (tokens, sizes, colors, spacing, shadows, components, layout). Drie types: `value-mismatch`, `token-mismatch`, `component-missing`.
- Drifts aggregeren in `drifts.md` (DEV / DESIGNER secties)
- Severity-bepaling per dimensie via vaste tolerantie-tabellen (kleuren, spacing, typografie, radius, shadows, iconen)

## Installatie

```bash
# Lokaal voor jezelf
mkdir -p ~/.claude/skills
cp -r figma-to-code ~/.claude/skills/

# Verifieer
claude
> /skills
# Zou figma-to-code moeten tonen
```

## Gebruik

### Eerste keer in een nieuwe projectrepo

```
$ cd /pad/naar/blis-project
$ claude
> /figma-to-code setup
```

Skill vraagt of het de `docs/`-structuur mag aanmaken. Bij bevestiging: kopieert
`tokens.md`, `components.md`, en het `components/`-template naar de projectrepo.

### Werkwijze permanent activeren in projectrepo

Na setup, voor permanente activatie in elke chat van het project:

```
> /figma-to-code init-claude-md
```

Skill toont een markdown-blok dat je in `CLAUDE.md` van de projectrepo kunt plakken.
Vanaf dan triggert de skill automatisch bij elke chat in dat project — zonder
slash-command.

### Component documenteren

```
> /figma-to-code map ConfirmationModal
```

Of natural language:

```
> Documenteer ConfirmationModal volgens onze figma-to-code werkwijze
```

Skill doorloopt A1-A5: inventariseren, tokens vullen, component-spec maken, mapping
aan Figma, klaar voor volgende component.

## Bestandsstructuur

```
figma-to-code/
├── SKILL.md                            ← werkwijze + drift-taxonomie + severity-tabellen
├── README.md                           ← dit bestand
├── templates/
│   ├── tokens.md                       ← leeg tokens-template (Figma-naam ↔ code-pad ↔ waarde)
│   ├── components.md                   ← leeg components-index template (incl. Figma-node reverse-lookup)
│   ├── component-spec.md               ← template voor één component-spec
│   ├── drifts.md                       ← leeg drift-aggregator template (DEV / DESIGNER)
│   ├── claude-md-snippet.md            ← prompt om in projectrepo CLAUDE.md te plakken
│   └── example-confirmation-modal/     ← ingevuld voorbeeld (Blis)
└── references/
    └── drift-and-sync.md               ← drift-typen + sync-richting tabel (deprecated; SKILL.md is leidend)
```

## Wat dit niet is

- **Geen design-system documentatie-tool.** Doel is mapping, niet een complete
  design-system-laag bouwen.
- **Geen drift-detectie als hoofdfunctie.** Drift wordt kort gemarkeerd; mapping is
  het primaire werk.
- **Geen vervanging voor Figma Code Connect.** Voor projecten waar Code Connect
  beschikbaar is en is opgezet, doet die de mapping automatisch. Deze skill is voor
  projecten zonder Code Connect.
- **Geen documentatie van gedrag.** Hover, focus, motion leven in code. Deze workflow
  mapt — de code beschrijft het gedrag.
