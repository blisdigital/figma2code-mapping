# Figma-to-Code skill

Mapping-skill voor ontwikkelaars die Figma-MCP-codegen ≥90% willen laten matchen met een bestaande codebase, zonder Figma Code Connect of Storybook in te zetten.

## Voor wie

Deze skill is een **developer tool**. Doelgroep: ontwikkelaars die in een React/Vue/Svelte/etc. codebase werken met een design-system in Figma, en willen dat Claude Code's Figma-MCP-output direct hun bestaande tokens, componenten en imports gebruikt — geen handmatig vertaalwerk per request.

**Twee-laagse setup:**

| Laag | Waar | Wat |
|---|---|---|
| **Skill (deze repo)** | `~/.claude/skills/figma-to-code/` (via symlink) | De werkwijze zelf — wordt automatisch geladen door Claude Code |
| **Output (per project)** | `<jouw-project>/docs/` of `<jouw-project>/Figma-to-code/figma-to-code-mapping/` | De mapping-docs die in jouw repo komen (`tokens.md`, `components.md`, per-component-specs, `drifts.md`, `verify-queue.md`) |

De skill is project-agnostisch geïnstalleerd op user-level; de mapping-output staat in iedere project-repo afzonderlijk.

## Wat het doet

Wanneer Claude Code in jouw project-repo werkt en deze skill triggert:

- Inventariseert je codebase (waar leven tokens, component-folders, styling-aanpak)
- Bouwt incrementeel een mapping op (`tokens.md`, `components.md`, per-component-specs)
- Vergelijkt elke Figma-instance met de code en markeert echte drift via een drift-test
- Houdt onbevestigde mappings vast in een `verify-queue.md` voor de volgende live-MCP-sessie
- Doorloopt een A1-A6 werkwijze: inventariseer → tokens → spec → Figma-mapping → recursive Uses → validation-checklist

Volledige werkwijze en regels: zie [SKILL.md](SKILL.md).

## Installatie

De aanbevolen aanpak is een **symlink** vanuit `~/.claude/skills/figma-to-code/` naar deze repo. Voordeel: één source of truth — wijzigingen via `git pull` zijn direct actief in elk project dat de skill gebruikt.

```bash
# Clone deze repo
git clone https://github.com/k-rutten/figma2code.git ~/Github/figma2code

# Symlink in ~/.claude/skills/
ln -s ~/Github/figma2code ~/.claude/skills/figma-to-code

# Verifieer
claude
> /skills
# Zou figma-to-code moeten tonen
```

Voor updates:
```bash
cd ~/Github/figma2code && git pull
```

## Gebruik

### Eerste keer in een nieuwe projectrepo

```
$ cd /pad/naar/project
$ claude
> /figma-to-code setup
```

Skill vraagt of het de `docs/`-structuur mag aanmaken. Bij bevestiging: kopieert `tokens.md`, `components.md`, `drifts.md`, `verify-queue.md` en het `components/`-template naar de projectrepo.

### Permanent activeren in projectrepo

```
> /figma-to-code init-claude-md
```

Toont een markdown-blok dat je in de project-`CLAUDE.md` plakt. Vanaf dan triggert de skill automatisch in elke chat — geen slash-command nodig.

### Component documenteren

```
> /figma-to-code map ConfirmationModal
```

Of natural language:

```
> Documenteer ConfirmationModal volgens onze figma-to-code werkwijze
```

Skill doorloopt A1-A6: inventariseren, tokens vullen, component-spec maken, Figma-mapping (incl. drift-test), recursive Uses, validation-checklist.

## Bestandsstructuur

```
figma2code/
├── SKILL.md                            ← werkwijze + hard rules + drift-taxonomie
├── README.md                           ← dit bestand (humans)
├── CLAUDE.md                           ← edit-conventies voor Claude in deze repo
└── templates/
    ├── tokens.md                       ← tokens-template (Figma-naam ↔ code-pad ↔ waarde)
    ├── components.md                   ← components-index template (incl. Figma-node reverse-lookup)
    ├── component-spec.md               ← template voor één component-spec
    ├── drifts.md                       ← drift-aggregator template
    ├── verify-queue.md                 ← `[VERIFY]`-queue template
    ├── claude-md-snippet.md            ← prompt-blok voor projectrepo CLAUDE.md
    └── example-confirmation-modal/     ← ingevuld voorbeeld
```

## Wat dit niet is

- **Geen design-system documentatie-tool.** Doel is mapping, geen complete design-system-laag.
- **Geen drift-detectie als hoofdfunctie.** Drift komt naar boven als bijproduct van mapping.
- **Geen vervanging voor Figma Code Connect.** Voor projecten waar Code Connect is opgezet, doet die de mapping automatisch. Deze skill is voor projecten zonder.
- **Geen documentatie van gedrag.** Hover, focus, motion, keyboard-handling leven in code, niet in specs.

Volledige skill-boundary inclusief routing naar zusterskills: zie [SKILL.md § Skill-boundary](SKILL.md#skill-boundary).
