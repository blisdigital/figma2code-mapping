# Deferred mapping-side fixes

Items that **belong in the mapping skill** but were not implemented in v3.0. Written after the v3.0 audit against Pelle's developer test feedback (2026-05-08).

These are **not** items deferred because they belong in the future `figma-to-code-implement` skill — those are listed in [IMPLEMENT-SKILL-PROPOSAL.md](IMPLEMENT-SKILL-PROPOSAL.md). The items below are mapping-discipline gaps the current skill should close.

---

## 1. Cache enrichment for auto-layout intent

**Source:** Pelle's #3 — "design hoeft niet EXACT te zijn. Bare MCP gaf comments over hoeveel-bij-hoeveel achtergrond moest zijn." Pelle saw responsiveness intent in MCP output that figma2code lost.

**Current state.** `figma-context/<node-id>.json` cache stores:
- Node metadata (name, type, dimensions, position)
- Variables consumed by the node (from `get_variable_defs`)
- Metadata-XML tree

It does **not** capture per-instance auto-layout properties (fill/hug, gap percentages, padding values, alignment). PR 4 (v2.11) added project-level auto-layout-conventions as mapping data — but the per-instance intent that needs translation is not preserved in cache.

**Why mapping-side, not implement-skill.** Caching what MCP returns is mapping-data capture (Hard rule #11 spirit: capture facts, don't lose them). Implement-skill cannot consume what mapping does not preserve.

**Proposed fix.**
- A4 MCP-fetch step also calls `get_design_context` and stores the auto-layout properties returned (e.g., `layout: { mode: "VERTICAL", primaryAxisSizingMode: "FIXED", counterAxisSizingMode: "AUTO", itemSpacing: 16, paddingLeft: 24, ... }`) in the cache JSON under a new `layout_intent` key.
- A6 validation: confirm `layout_intent` is present in cache for nodes with auto-layout.
- No mapping-table change — this is cache enrichment for downstream consumption.

**Estimated impact:** small SKILL.md edit to A4 + cache JSON schema extension. ~20 lines.

---

## 2. Hard rule about skill output locations

**Source:** Pelle's #2 — "de figma2code publiceerde in een random file opens de figma context key, had ik ff gemist". Pelle saw the skill writing somewhere unexpected.

**Current state.** PR #20 v3.0 added `.gitignore` setup-step for `figma-context/`. That covers cache. But the skill currently has no rule **forbidding** writes outside the standardized paths. If the LLM during a mapping pass decides to write debug output, scratch files, or auxiliary data somewhere else, nothing stops it.

**Why mapping-side, not implement-skill.** Output-discipline is meta-skill behavior, not emit-discipline. Both mapping and implement skills should respect a write-perimeter.

**Proposed fix as Hard rule #12:**

```markdown
12. **Output to standardized paths only.** The skill writes to:
    - `docs/tokens.md`, `docs/components.md`, `docs/drifts.md`,
      `docs/verify-queue.md` (or repo-equivalent paths)
    - `<component-folder>/<name>.md` for per-component specs
    - `figma-context/<node-id>.json` for cache
    - Project `.gitignore` (one-time, only during setup)

    Never write debug output, scratch files, temporary auxiliary data, or
    notes to other locations. If the agent needs to record something
    not covered by the above, ask the user where it should live —
    don't improvise a file path.
```

**Estimated impact:** SKILL.md Hard rules expanded from 11 to 12. Symmetrical with existing rules in scope-discipline. ~10 lines.

---

## Status

These two are not blockers for v3.0 to be useful. They are gaps in mapping-side discipline that should land in v3.1 or v3.2 before significant new mapping passes are run on additional projects.

**Priority:** medium — neither has caused a failure on the projects mapped so far, but both are pre-empting categories of failure that have one anecdotal data point.

**Owner:** unassigned. Open to PR.
