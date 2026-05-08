# Deferred mapping-side fixes — audit log

Audit of mapping-side gaps after v3.0. Re-evaluated against existing skill content; both initially proposed items turned out to be **already covered or non-critical**. This file is kept as an audit record (so we don't propose them again) rather than a fix-list.

For implementation-side concerns (refuse-raw, single-API enforcement, auto-layout translation at emit, search-and-adopt), see `IMPLEMENT-SKILL-PROPOSAL.md` in the sibling `figma2code-implement` repo.

---

## #1 Output-location discipline — already adequate

**Initial concern.** Developer test reported the skill writing the Figma context key into an unexpected file. Initial proposal: add Hard rule #12 forcing all skill output to standardized paths.

**Audit result: convention is already in the skill, no Hard rule needed.**

| Output | Documented in SKILL.md | Strict? |
|---|---|---|
| `docs/tokens.md`, `components.md`, `drifts.md`, `verify-queue.md` | "The documents" table + intro paragraph | implicit |
| `<component-folder>/<name>.md` (per-component specs co-located) | "Co-location convention" sub-section | explicit |
| `figma-context/<node-id>.json` (cache) | "Cache + hash check" sub-section + setup-step `.gitignore` | explicit + operational |

A skill-following LLM has no documented place to write outside these paths. The negative rule ("never write elsewhere") is implicit. The reported failure was likely a pre-rename, pre-v3.0 bug — not a gap the new skill permits.

**Decision: do not add Hard rule #12.** Convention is adequate. Belt-and-suspenders without evidence of recurring gap.

---

## #2 Cache enrichment with auto-layout intent — gap exists, work-around adequate

**Initial concern.** `figma-context/<node-id>.json` cache stores variables + metadata-XML + mapping-link, but does not store per-instance auto-layout properties (`layoutMode`, `primaryAxisSizingMode`, `itemSpacing`, padding values) returned by `get_design_context`. Initial proposal: enrich cache with `layout_intent` key.

**Audit result: gap is real, but the implement-skill workaround makes it non-critical.**

The implement-skill (per `IMPLEMENT-SKILL-PROPOSAL.md` §B3 in the sibling `figma2code-implement` repo) does a **live MCP fetch** on the target Figma frame before emit. It does not rely on cached layout-intent — it fetches fresh per emit-pass. Cache serves the mapping pass; emit serves itself.

**Decision: do not enrich cache for layout-intent.** Implement-skill will fetch live. If a future workflow surfaces where cached layout-intent is actually consumed (e.g., bulk-validation across many components), revisit.

---

## Open mapping-side items: none

After this audit, no mapping-side gaps remain that should be addressed in the v3.x mapping skill. The remaining concerns from developer-test feedback that are still unaddressed are all implementation-side and live in `IMPLEMENT-SKILL-PROPOSAL.md` (sibling `figma2code-implement` repo):

- Refuse-emit raw values where token-path exists
- Single-styling-API enforcement at emit
- Apply auto-layout conventions at emit time
- Hoist truly-needed-once values to page-level CSS variables
- Search-and-adopt patterns for non-componentized layouts

These belong in `figma-to-code-implement` (TBD), not in the mapping skill.

---

## Process note

This audit is itself a useful artefact — both initial proposals sounded like real gaps before being checked against the skill's existing content. Going forward: before adding to a "deferred fixes" list, grep the skill for related rules first. If convention exists, document the convention rather than propose a duplicate.
