# Figma-to-Code Mapping skill

Mapping skill for developers who want Figma MCP code generation to match an existing codebase ≥90%, without having to set up Figma Code Connect or Storybook.

Scope is **mapping only** — the skill documents the relationship between Figma and existing code. It does not generate code or enforce emit-discipline. The emit half is covered by the sister skill [`figma-to-code-implement`](https://github.com/blisdigital/figma2code-implement).

## Vision

**A Figma file is full of information, but not specifications.** Without an explicit mapping layer, AI tools that consume Figma have to guess at everything the file does not say — token assignments, component identity, variant axes, drift between intent and rendered output. This skill closes that gap by documenting the relationship as data.

**Goal: tight alignment between code and Figma.** Code is source of truth, Figma is intent — the skill keeps these two close together via five mechanisms:

1. **Token mapping** (`docs/tokens.md`) — Figma variable → code path → value, with explicit status per row (`match` / `value-mismatch` / `figma-missing` / `code-missing`)
2. **Component mapping** (`docs/components.md` + per-component specs co-located next to `<name>.tsx`) — atomic-design index, Uses column, variant and naming aliases
3. **Drift as decision point** (`docs/drifts.md`) — not silently resolved but marked with severity (Critical/Major/Minor) and owner (DEV/DESIGNER/DEV+DESIGNER) so designer or dev picks a resolution
4. **Verify queue** (`docs/verify-queue.md`) — unconfirmed mappings until the next live-MCP session, prevents false `figma-missing` conclusions
5. **Hard Rule "consume existing"** — every Figma element matches first against existing primitives before something new is proposed; the A1-A6 method enforces this order

Together these five deliver the ≥90%: token utilities resolve to the right CSS vars, primitives are consumed (no duplicates), variants map via an explicit table, and drift does not pile up silently.

## For whom

This skill is a **developer tool**. Audience: developers working in a React/Vue/Svelte/etc. codebase with a design system in Figma, who want Claude Code's Figma MCP output to use their existing tokens, components, and imports directly — no manual translation per request.

**Two-layer setup:**

| Layer | Where | What |
|---|---|---|
| **Skill (this repo)** | `~/.claude/skills/figma-to-code-mapping/` (via symlink) | The method itself — automatically loaded by Claude Code |
| **Output (per project)** | `<your-project>/docs/` or `<your-project>/Figma-to-code/figma-to-code-mapping/` | The mapping docs that land in your repo (`tokens.md`, `components.md`, per-component specs, `drifts.md`, `verify-queue.md`) |

The skill is project-agnostic and installed at user level; the mapping output lives in each project repo separately.

## What it does

When Claude Code works in your project repo and this skill triggers:

- Inventories your codebase (where tokens live, component folders, styling approach)
- Builds a mapping incrementally (`tokens.md`, `components.md`, per-component specs)
- Compares each Figma instance with the code and marks real drift via a drift test
- Holds unconfirmed mappings in a `verify-queue.md` for the next live-MCP session
- Walks through an A1-A6 method: inventory → tokens → spec → Figma mapping → recursive Uses → validation checklist

Full method and rules: see [SKILL.md](SKILL.md).

## Installation

The recommended approach is a **symlink** from `~/.claude/skills/figma-to-code-mapping/` to this repo. Benefit: one source of truth — changes via `git pull` are immediately active in every project that uses the skill.

```bash
# Clone this repo
git clone https://github.com/blisdigital/figma2code-mapping.git ~/Github/figma2code-mapping

# Symlink into ~/.claude/skills/
ln -s ~/Github/figma2code-mapping ~/.claude/skills/figma-to-code-mapping

# Verify
claude
> /skills
# Should show figma-to-code-mapping
```

To update:
```bash
cd ~/Github/figma2code-mapping && git pull
```

## Usage

### First time in a new project repo

```
$ cd /path/to/project
$ claude
> /figma-to-code-mapping setup
```

The skill asks whether it may create the `docs/` structure. On confirmation: copies `tokens.md`, `components.md`, `drifts.md`, `verify-queue.md`, and the `components/` template into the project repo.

### Permanently activate in project repo

```
> /figma-to-code-mapping init-claude-md
```

Shows a markdown block to paste into the project `CLAUDE.md`. From then on the skill triggers automatically in every chat — no slash command needed.

### Document a component

```
> /figma-to-code-mapping map ConfirmationModal
```

Or natural language:

```
> Document ConfirmationModal following our figma-to-code-mapping method
```

The skill walks through A1-A6: inventory, fill tokens, create component spec, Figma mapping (incl. drift test), recursive Uses, validation checklist.

## File structure

```
figma2code/
├── SKILL.md                            ← method + hard rules + drift taxonomy
├── README.md                           ← this file (humans)
├── CLAUDE.md                           ← edit conventions for Claude in this repo
└── templates/
    ├── tokens.md                       ← tokens template (Figma-name ↔ code-path ↔ value)
    ├── components.md                   ← components-index template (incl. Figma-node reverse lookup)
    ├── component-spec.md               ← template for a single component spec
    ├── drifts.md                       ← drift-aggregator template
    ├── verify-queue.md                 ← `[VERIFY]`-queue template
    ├── claude-md-snippet.md            ← prompt block for project-repo CLAUDE.md
    └── example-confirmation-modal/     ← filled example
```

## Prerequisites — outside this skill

**Figma hygiene matters.** This skill maps an existing Figma file to existing code. The quality of the mapping is bounded by the quality of the Figma source: clear frame names, semantic auto-layout, consistent variant decomposition. Messy Figma → messy MCP output → messy code, regardless of how well the mapping is documented. Cleaning up Figma is upstream design-ops work, outside this skill's scope.

**Existing styleguide assumed.** The skill assumes a codebase with tokens (CSS variables, theme object, or Tailwind config) and component primitives already in place. Greenfield projects without a styleguide are out of scope — start there first, then layer mapping on top.

## What this is not

- **Not a design-system documentation tool.** The goal is mapping, not a complete design-system layer.
- **Not a replacement for Figma Code Connect — a complement instead.** Code Connect binds Figma components to code snippets via `.figma.ts` files in the repo (requires a Figma Organization seat). This skill works freemium and with markdown, and adds something Code Connect does not: **drift detection as a loop**. Code Connect maps one-to-one; this skill detects when code and Figma drift apart and logs it as a decision point for designer or dev. The two approaches combine — Code Connect for mapping publication to Figma Dev Mode, this skill for the drift loop.
- **Not behavior documentation.** Hover, focus, motion, keyboard handling live in code, not in specs.
- **Not code generation or emit-discipline.** This skill maps; it documents the relationship between Figma and existing code. Enforcing rules at code-emit time (refusing hardcoded values, picking layout primitives, translating auto-layout, search-and-adopt patterns) lives in the sister skill [`figma-to-code-implement`](https://github.com/blisdigital/figma2code-implement).
- **Not a Figma-hygiene fixer.** If a Figma frame visually resembles a component but is not an instance of the master, the skill does not infer the similarity and link automatically. That is upstream design-ops work. Mapping classifies the frame as element-frame (token-only) or surfaces it for deliberate decision; designer fixes Figma hygiene.

Full skill boundary including routing to sister skills: see [SKILL.md § Skill boundary](SKILL.md#skill-boundary).

## Sister skill — figma-to-code-implement

Mapping is the data layer; [`figma-to-code-implement`](https://github.com/blisdigital/figma2code-implement) is the policy layer. They are one pipeline:

```
User: build this Figma frame
  → implement skill triggers
    → B1 mapping presence check
      → mapping exists → consume + emit
      → no mapping → halt + route to /figma-to-code-mapping map X
```

Routing is bi-directional: implement halts and routes to mapping on gaps; mapping (Hard rule #2) routes implement-intent sentences to implement.

**Use both together.** Map first, then implement. Mapping documents the relationship; implementation enforces it at code-emit time.
