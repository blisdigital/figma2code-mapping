# Figma-to-Code Mapping skill

> **v3.0 — renamed from `figma-to-code` to `figma-to-code-mapping`.** Explicit
> half-of-pipeline name. The skill maps Figma to existing code; it does not
> generate code or enforce emit-discipline. A separate `figma-to-code-implement`
> skill is on the roadmap for the emit half.
>
> Existing projects using `/figma-to-code` slash commands and a
> `~/.claude/skills/figma-to-code` symlink need a one-time migration —
> see [Migration from v2.x](#migration-from-v2x) below.

Mapping skill for developers who want Figma MCP code generation to match an existing codebase ≥90%, without having to set up Figma Code Connect or Storybook.

## Vision

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
git clone https://github.com/blisdigital/figma2code.git ~/Github/figma2code

# Symlink into ~/.claude/skills/
ln -s ~/Github/figma2code ~/.claude/skills/figma-to-code-mapping

# Verify
claude
> /skills
# Should show figma-to-code-mapping
```

To update:
```bash
cd ~/Github/figma2code && git pull
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
- **Not code generation or emit-discipline.** This skill maps; it documents the relationship between Figma and existing code. Enforcing rules at code-emit time (refusing hardcoded values, picking layout primitives, translating auto-layout, search-and-adopt patterns) belongs in a separate **figma-to-code-implement** skill — on the roadmap, not in this skill.

Full skill boundary including routing to sister skills: see [SKILL.md § Skill boundary](SKILL.md#skill-boundary).

## Migration from v2.x

The skill was renamed from `figma-to-code` to `figma-to-code-mapping` in v3.0 to clarify that this skill maps Figma to code — it does not generate or emit code. A separate `figma-to-code-implement` skill is on the roadmap for the emit half.

If you have an existing v2.x setup, run a one-time migration:

```bash
# 1. Pull the latest skill
cd ~/Github/figma2code && git pull

# 2. Replace the symlink
rm ~/.claude/skills/figma-to-code
ln -s ~/Github/figma2code ~/.claude/skills/figma-to-code-mapping

# 3. Update slash commands in any project CLAUDE.md
#    /figma-to-code map     → /figma-to-code-mapping map
#    /figma-to-code setup   → /figma-to-code-mapping setup
#    /figma-to-code init-claude-md → /figma-to-code-mapping init-claude-md
```

Existing project mapping output (`tokens.md`, `components.md`, per-component specs, `drifts.md`, `verify-queue.md`) requires no changes. Only the skill name and slash commands change.
