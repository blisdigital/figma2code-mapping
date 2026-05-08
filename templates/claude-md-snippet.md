# Claude-md snippet for the project repo

When a user invokes `/figma-to-code-mapping init-claude-md`, show the following markdown block plus instructions. **Do not write to CLAUDE.md yourself.** The user pastes it.

---

## What to show the user

A short intro:

> Here is the markdown block you can paste into `CLAUDE.md` of your project repo (root).
> After that, the figma-to-code-mapping skill triggers automatically in every chat in this project —
> without having to type `/figma-to-code-mapping`.
>
> **Placement:**
> - If `CLAUDE.md` does not yet exist in the project-repo root, create it with this block as content
> - If `CLAUDE.md` already exists, append this block at the end

Then the block in a markdown code fence:

````markdown
## Figma-to-code method

This project uses the figma-to-code-mapping skill for mapping between Figma designs and code.
For any work on components, tokens, or Figma mapping: use the figma-to-code-mapping skill.

**Method in short:**
- Code is source of truth, Figma is intent
- Five documents keep the mapping:
  - `docs/tokens.md` — three-column token mapping
  - `docs/components.md` — atomic-design index with Uses relations
  - `<component-folder>/<name>.md` — per-component mapping spec (co-located)
  - `docs/drifts.md` — aggregated drift-test passers
  - `docs/verify-queue.md` — unconfirmed mappings for the next live-MCP session
- Read before write, no improvising on gaps, organisms first on selection
- Drift is briefly marked via the drift test, not extensively reported

**Full method:** `~/.claude/skills/figma-to-code-mapping/SKILL.md`

**Slash commands:**
- `/figma-to-code-mapping map <component>` — document a component (full A1-A6)
- `/figma-to-code-mapping setup` — create `docs/` structure (first time only)
````

## What the user does next

1. Copies the block
2. Opens `CLAUDE.md` in the project-repo root (or creates it)
3. Pastes the block at the end
4. Commits to git so it works team-wide
5. On the next chat in this project the skill triggers automatically

## What you do NOT do

- Do not write to `CLAUDE.md` in the project repo yourself
- Do not run `git add` or `git commit`
- Do not assume whether `CLAUDE.md` already exists — let the user check
