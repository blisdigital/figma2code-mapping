# Implement-skill handoff — coordination items

> Coordination document between `figma2code-mapping` (this repo) and `figma2code-implement` (sister repo, design plan in [PR #1](https://github.com/blisdigital/figma2code-implement/pull/1)).
>
> The implement skill is being designed in `figma2code-implement`. For the two skills to feel like one pipeline, four small changes are needed here. **Not yet applied** — apply when implement ships v0.1.
>
> Status: design captured, awaiting implement skill v0.1 ship before merging.

---

## Context

`figma-to-code-mapping` is the **data layer** (document the relationship between Figma and code). `figma-to-code-implement` is the **policy layer** (enforce that relationship at code-emit time). Mapping enables; implement enforces.

The two are designed as one pipeline:

```
User: build this Figma frame
  → implement skill triggers
    → B1 mapping presence check
      → mapping exists for this node → consume + emit
      → no mapping → halt + route to /figma-to-code-mapping map X
```

Routing **implement → mapping** is geregeld in implement's B1-B3 (halt + slash-command). Routing **mapping → implement** is currently **not** wired — mapping says "out of scope, see future skill" but does not concretely point. This document captures the four edits that close that loop.

---

## The four changes

### 1. Skill boundary table — concrete route

**Where:** `SKILL.md § Skill boundary` (currently line 67-79)

**Current state:**

| Scenario | This skill? | Otherwise: |
|---|---|---|
| **Implement** Figma frame as working code | ❌ no | Out of scope. This skill builds the mapping only; code generation is downstream work and belongs in a separate skill |

**Proposed change:**

| Scenario | This skill? | Otherwise: |
|---|---|---|
| **Implement** Figma frame as working code | ❌ no | Out of scope — use `figma-to-code-implement` (consumes this skill's output for code emit) |

**Why:** turns an aspirational "future skill" reference into a concrete route. Once implement ships, this row directs the user without ambiguity.

---

### 2. Rule #2 trigger box — implement-intent clausule

**Where:** `SKILL.md § Hard rules` rule #2 box (currently line 45-52)

**Current state:** the box distinguishes "Do NOT treat as code-update trigger" (Figma auto-clipboard text, bare URL pastes) from "Do treat" (explicit per-token sentences, reviewed PR).

**Proposed addition** — third bullet group:

```markdown
> **Implement-intent sentences route to `figma-to-code-implement`:**
> - "build this Figma frame"
> - "implement this design"
> - "generate code for [Figma URL]"
> - "make this component"
>
> Mapping skill: stop, suggest user runs `/figma-to-code-implement <node>` instead.
> (Implement will halt itself on missing mapping and route back — closed loop.)
```

**Why:** today a user typing "implement this design" while in a mapping session gets the mapping flow incorrectly. Mapping should recognise these sentences and route, not absorb them.

**Coupled with:** implement skill open decision #6 (same trigger list, mirrored semantics). Both skills must use the same trigger sentences to avoid divergence.

---

### 3. Optional auto-handoff after `/figma-to-code-mapping map X`

**Where:** new optional step in `SKILL.md § Slash commands` or end of A5/A6 description.

**Proposed addition:**

After completing `/figma-to-code-mapping map X` (A1-A6 walk-through), the skill optionally offers:

```
Mapping complete for X. Run `/figma-to-code-implement X` now to emit code from this mapping?
[y/N]
```

User confirms → skill exits, prompts implement skill activation.
User declines → silent return.

**Why:** today the flow is mapping → user manually re-triggers implement. Friction. Parallel to mapping's existing A5 recursive Uses pattern (auto-continue without separate permission).

**Caveat:** opt-in only. Some mapping sessions are documentation-only (no implementation intent). Don't push.

---

### 4. Cache schema formalisation

**Where:** `SKILL.md § Cache + hash check` (currently line 113-123, one example block)

**Current state:** schema is shown as a single JSON example with informal field descriptions in surrounding prose.

**Proposed change:** add a typed schema specification:

```markdown
### Cache file schema

`figma-context/<node-id>.json` — required fields unless marked optional.

| Field | Type | Required | Description |
|---|---|---|---|
| `node_id` | string (Figma ID format) | ✓ | Figma node identifier |
| `name` | string | ✓ | Human-readable Figma node name |
| `mapped_to_component` | string | ✓ | Code-component name in components.md |
| `spec_path` | string (relative path) | ✓ | Co-located spec file relative to repo root |
| `spec_synced_with_code_at` | ISO 8601 datetime | ✓ | Last sync timestamp |
| `spec_synced_with_files_hash` | string (`sha256:<hex>`) | ✓ | Hash of code files at sync time |
| `master_verified_via` | string enum: `"direct"` \| `"instance-id-format"` | optional | Master verification method (see below) |
| `cache_verified_via_mcp` | boolean | optional | False if cache populated without live MCP |
```

**Why:** implement skill is a strict consumer of this cache. Schema changes silently break implement. A formal contract makes the dependency explicit and the schema versioning intentional.

---

## Other coordination items (no overlap, just alignment)

### `templates/claude-md-snippet.md` — additive composition

When implement ships, its `init-claude-md` will produce a similar snippet. They should compose, not duplicate. Suggested pattern for implement:

```markdown
## Figma-to-code implementation
This project uses the figma-to-code-implement skill for emit-time enforcement.
Builds on the figma-to-code-mapping skill's output.
[...]
```

User can paste both snippets — implement detects mapping snippet presence and shows only the additive part.

### README cross-links

Both READMEs add a short "Sister skill" section. Three-sentence flow explanation + link.

---

## Application timing

**Do not merge this PR yet.** These edits assume `figma-to-code-implement` exists as a runtime skill — currently it's only a design plan ([PR #1](https://github.com/blisdigital/figma2code-implement/pull/1)).

**Merge sequence:**

1. Implement skill ships v0.1 (SKILL.md + frontmatter + slash commands installable via symlink)
2. Apply edits 1-4 here in mapping repo
3. Cross-test the loop: `/figma-to-code-implement X` on un-mapped node → halt + route → mapping `/figma-to-code-mapping map X` → optional auto-handoff back → implement emit

Until step 1: this PR is a **review document** for the design, not a merge candidate.

---

## Status

**Design captured. Awaiting implement v0.1 ship.**

Owner: Kevin Rutten.

Tracking implement skill design: [figma2code-implement#1](https://github.com/blisdigital/figma2code-implement/pull/1) — § Koppeling met mapping skill.
