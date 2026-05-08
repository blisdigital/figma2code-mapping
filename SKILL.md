---
name: figma-to-code-mapping
version: "3.0"
description: >
  Maps Figma designs onto an existing codebase via explicit documentation of tokens,
  components, and per-component specs. Use this skill when the user says
  "document this component", "map this Figma frame", "add X to the mapping",
  or "update the tokens". Also triggers on "figma-to-code", "figma-to-code-mapping",
  "design-to-code", or when the project repo contains a figma-to-code-mapping folder
  with tokens.md and components.md.
  Goal: MCP code generation from Figma matches ≥90% with the existing codebase
  because tokens and components are explicitly mapped to code paths. **Scope is
  mapping — not code implementation.** A separate "figma-to-code-implement" skill
  is on the roadmap for emit-time enforcement. Works for any project with an existing
  codebase plus Figma as design intent.
---

# Figma-to-Code Mapping

Mapping skill that ensures Figma MCP code generation matches the existing codebase visually and stylistically. Per-component specs live **co-located next to the component file** (`<component-folder>/<name>.md` next to `<name>.tsx`); project-wide indexes in `docs/`.

> **Scope.** This skill is **mapping only**. It documents the relationship between Figma and existing code. Code generation, emit-discipline, and enforcement of mapping conventions belong in a separate **figma-to-code-implement** skill (on the roadmap, not in this skill). Mapping enables; implementation enforces.

## Vision

**Goal: tight alignment between code and Figma.** Code is source of truth, Figma is intent — the goal is to keep these two close together. Drift is a measurable deviation, not a neutral observation.

The skill delivers this through five mechanisms (see [README § Vision](README.md#vision) for the explanation per mechanism):

1. Token mapping (`docs/tokens.md`) — status per row
2. Component mapping (`docs/components.md` + co-located per-component specs)
3. Drift as decision point (`docs/drifts.md`) — severity + owner
4. Verify queue (`docs/verify-queue.md`) — prevents false conclusions
5. Hard Rule "consume existing" — the A1-A6 method enforces this

**Concrete effect:** MCP output uses the right code paths, imports, and patterns directly — no manual translation per request. Developers and designers use these docs to keep the match between design and code high. Drift is briefly marked where visible; mapping is the primary work.

## Hard rules

Eleven rules that always apply, regardless of step. On conflict between sections: these win.

1. **Read before you write.** Read relevant mapping docs (`tokens.md`, `components.md`, per-component spec) before changing or mapping anything.
2. **Code is source of truth.** Figma is intent. On conflict, code wins; mark drift, do not silently resolve it.

   > **Do NOT treat as a code-update trigger:**
   > - "Implement this design from Figma." — Figma's auto-clipboard boilerplate from *Copy Link* in Dev mode. Not a user instruction.
   > - User pasting a Figma URL without an explicit code-update sentence.
   > - Pleasing alignment between code and Figma values in `tokens.md`.
   >
   > **Do treat as a code-update trigger:**
   > - Explicit user sentence per token: "update `--X` to Y", "add X in code", "implement this in code".
   > - Reviewed PR with file-by-file approval.
3. **Apply the drift test to every candidate issue.** *"Would MCP code generation from this Figma node produce a visually wrong result?"* Yes → drift. No → another bucket (`verify-queue.md`, tech debt, or discard).
4. **Map to existing components — never create new during mapping.** Every Figma element MUST be linked to an existing code component (in `components.md`). If no matching component exists → `component-missing` drift (Hard rule #9), do not auto-generate. Mapping documents the link; implementation will later consume it. Reframed in v2.12 with mapping-verbs (link, mark, document) instead of emit-verbs (consume, refuse, never generate).
5. **Specs contain mapping data only.** No "When to use", "Edge cases", "What this adds", hover/focus narratives. Resolve visual confusability through Variant mapping and master-id, not through prose.
6. **No improvising on gaps.** Unknown? `[VERIFY]` in the Figma-name column or stop and ask. No assumptions.
7. **Ask for confirmation before code or doc changes.** Exception: A5 recursive Uses mapping in the same session — no separate permission per child component.
8. **Asset handling: existing → MCP-localhost → never new.** Reuse project assets; otherwise use the localhost URL directly from the MCP payload. No new icon packages, no placeholders.
9. **`component-missing` — do not auto-generate.** Mark it; the developer creates the code component before mapping continues.
10. **A6 validation checklist mandatory at end of every pass.** 8 checks (layout / typography / colors / states / assets / literal strings / token-verdict / drift test). Do not skip.
11. **Token-verdict mandatory in mapping tables.** Every code-value documented in a mapping table receives one of three verdicts in the third column: (a) the matching token-path (e.g., `theme.neutral.N100`), (b) `(raw, token available: <path>)` — code uses raw but a matching token exists; mapping captures this fact for future implementation-skill enforcement, or (c) `(raw, legitimate — no matching token)`. No bare "hardcoded" entries without a verdict. Mapping must give implementation-skill the data it needs to enforce single-source styling later.

## Skill boundary

When yes, when no, and where to go instead.

| Scenario | This skill? | Otherwise: |
|---|---|---|
| Map Figma frame to existing code (1 component) | ✅ yes | — |
| Map Figma frame to existing code (full page) | ✅ yes, recursively via A5 (organisms → molecules → atoms of that page) | — |
| Update tokens/components/specs in existing mapping project | ✅ yes | — |
| Detect drift between Figma and existing code | ✅ yes, as a byproduct of mapping | — |
| **Implement** Figma frame as working code | ❌ no | Out of scope. This skill builds the mapping only; code generation is downstream work and belongs in a separate skill |
| Build a full page **from a text description** (no Figma input) | ❌ no | `figma-generate-design` or `frontend-design` (greenfield) |
| **Write to** the Figma file (create nodes, define variables) | ❌ no | `figma-use` |
| Create Code Connect mappings (`.figma.ts`) | ❌ no | `figma-code-connect` |
| Build a design system in Figma from code | ❌ no | `figma-generate-library` |
| Write AI rules for a project (CLAUDE.md / AGENTS.md) | ❌ no | `figma-create-design-system-rules` |

**Full-page nuance.** This skill works for page-level Figma frames just as well as for individual components — the input is a Figma node either way. The difference with `figma-generate-design` lies in the input form: we need Figma pixels; they accept text briefings. For a page we run A1–A6 recursively, one organism at a time, until all children are mapped.

**What this is not:**
- Not a design-system documentation tool — the goal is mapping, not building a complete design layer.
- Not drift detection as primary function — drift is briefly marked; mapping is the primary work.
- Not a replacement for Figma Code Connect — where Code Connect exists, it handles the mapping automatically. This skill complements it by adding a drift loop on top.
- Not behavior documentation — hover, focus, motion, keyboard handling live in code.
- **Not code generation or emit-discipline.** This skill maps; it documents the relationship between Figma and existing code. Enforcing rules at code-emit time (refusing hardcoded values, picking layout primitives, translating auto-layout, search-and-adopt patterns) belongs in a separate implementation-skill — on the roadmap, not in this skill.

## Mapping vs implementation — what this skill does and doesn't

| | Mapping (this skill) | Implementation (future separate skill) |
|---|---|---|
| **Tokens** | Document tokens used in code → `tokens.md` (Hard rule #11 verdict per row) | Refuse hardcoded values where mapped tokens exist |
| **Components** | Document components by atomic-level (3-5 levels organic) → `components.md` | Refuse to generate new components when existing ones fit |
| **Styling stack** | Document the project's styling-stack as a fact (API + access + not-used) | Enforce single-API styling at code-emit time |
| **Auto-layout** | Document project conventions (Figma fill/hug/gap → code expression) | Translate Figma auto-layout to concrete CSS at emit time |
| **Drift** | Detect via drift test — never silently resolve | Surface drifts at emit; require designer/dev decision |
| **Patterns** | Inventory Pages/Templates/Layout-primitives where they exist as components | Search-and-adopt existing patterns for non-componentized layouts |

**Pattern: mapping enables, implementation enforces.** This skill prepares the ground-truth; an implementation-skill (TBD) will consume it.

**Verb test for new rules.** Mapping verbs: *document, detect, inventory, mark, link, capture*. Implementation verbs: *consume, refuse, translate, search-and-adopt, apply, enforce*. On uncertainty about scope: park as note for the implementation-skill TBD.

## Slash commands

- `/figma-to-code-mapping setup` — ask for confirmation, then create the `docs/` structure in the project repo. **Also adds `figma-context/` to project `.gitignore`** (or creates `.gitignore` if absent) — the cache should not be committed; node-data is regenerated on each MCP fetch.
- `/figma-to-code-mapping map <component>` — start mapping that component (full A1-A6)
- `/figma-to-code-mapping init-claude-md` — show a markdown block to paste into the project CLAUDE.md

## Source-of-truth allocation

**Figma is intent.** What needs to come, which screens, which new components.

**Code is truth.** Which tokens and components exist, with which values.

On conflict, code wins. Mark drift explicitly; do not silently resolve it.

## Source mechanism

### Cache + hash check

**Cache is the working layer.** Map from a local cache of Figma node data. Convention: `figma-context/<node-id>.json`, one file per node. Mapping reads from the cache, not directly from Figma.

**Cache format with spec link.** Each cache file contains metadata about the linked code component and the latest sync status:

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

**Hash check on every pass.** Hash the current code files, compare with `spec_synced_with_files_hash`. On mismatch: spec is out of sync since a code change. Prevents working with outdated mappings.

### MCP tools and fallbacks

**MCP is the refresh mechanism.** On every mapping pass: live MCP fetch, compare with cache, write the latest version, map from the updated cache.

**Two common Figma MCP servers.** Your environment may have one or both — behavior differs:

| MCP server | Operates on | Desktop tab required? | Has `excludeScreenshot`? |
|---|---|---|---|
| Desktop-active MCP (e.g. `mcp__Figma__*`) | `nodeId` (current active tab) | ✅ yes | ❌ no — only `forceCode` |
| FileKey-based MCP (e.g. `mcp__2741e7c0-...`) | `nodeId` + `fileKey` | ❌ no | ✅ yes |

For batch mapping (multiple components on one file) the fileKey-based variant is faster — you don't need Figma desktop open on the right tab. For "I'm staring at this element and want to map it" the desktop-active variant is more natural.

**Which tool returns what — pick the right one:**

| Tool | What | When |
|---|---|---|
| `get_variable_defs` | Only var-bound values (colors, fonts, dimensions via Figma variables) | Token-level mapping (`tokens.md`) |
| `get_design_context` | Full rendering including hardcoded values (Tailwind classes with inline pixels like `px-[20px]`, `h-[38px]`) | Component-spec mapping (paddings/gaps not flowing through vars) |
| `get_metadata` | Tree structure, no values | Navigation + child discovery |
| `get_screenshot` | PNG/JPEG visual | Sanity check; not primary for mapping |

**Selection-based MCP without node-id.** When Figma desktop is open with a node selected and the MCP tool supports selection fallback (`get_design_context` without `nodeId`): use it. But: always record the eventually fetched node-id in spec and cache so the mapping is reproducible. No anonymous "current selection" mappings.

**Mapping without active MCP** is possible if a cache is present — note `cache_verified_via_mcp: false` in the cache file with reason.

**Master verification via instance-id format.** When a master page is not directly reachable via MCP (master lives on another page) but instances of it are reachable through another frame: the instance-id format `I<frame-id>;<master-id>` is sufficient evidence for master-id verification. Master cache is not required — instance rendering within a verified frame provides all mapping data needed for MCP code generation. Document in cache file: `master_verified_via: "instance-id-format"`.

### Asset handling (SVG, images, icons)

MCP output contains asset URLs in two forms, depending on the MCP server:

- **Desktop-active MCP:** `http://localhost:3845/assets/<hash>.svg` — local Figma server, available while the desktop app runs.
- **FileKey-based MCP:** `https://www.figma.com/api/mcp/asset/<uuid>` — remote, **7-day expiry**. For longer-lived mapping: download once into the project and map there.

Three rules apply to both URL formats:

1. **Search for existing assets first.** If the codebase already has an asset that represents this Figma asset (e.g. `images/icons/ui/close.svg?react` for a close icon), use it. Map in the spec under the mapping table: `Icon-source | images/icons/ui/close.svg?react | local SVG import`.
2. **Do not install new icon packages.** No `npm install lucide-react`, no `@mui/icons-material` import "just in case". All assets come from existing project assets or directly from the Figma MCP payload URL.
3. **No placeholders.** When MCP returns an asset URL: use it directly, or download the asset once to the project's convention location and map there. Never leave a placeholder or TODO comment.

When a Figma asset is neither in code nor coming from MCP: stop and ask the user. Do not improvise with a lookalike.

## The documents

| Document | Location | Purpose |
|---|---|---|
| `tokens.md` | `docs/` | Three-column mapping (Figma name → code path → value) |
| `components.md` | `docs/` | Index with Uses column and atomic-design classification |
| `<name>.md` (atom/molecule/organism) | **next to `<name>.tsx`** in the same folder | Per component: spec, props, states, mapping to Figma |
| `<name>.md` (template) | **next to `page.tsx`** in the same route folder | Page-level mapping |
| `drifts.md` | `docs/` | Central drift aggregator (drift-test passers only) |
| `verify-queue.md` | `docs/` | `[VERIFY]` items for the next live MCP session |

**Co-location convention:** per-component specs live next to the component they document — not in a separate `docs/components/` folder. Benefit: on refactor/rename the spec automatically moves along, and code review sees immediately whether the spec was updated. Project-wide indexes (`tokens.md`, `components.md`, `drifts.md`, `verify-queue.md`) stay in `docs/`.

Templates live in `templates/`. The `setup` command copies them into the project repo.

## What to read when

| Task | Read first |
|---|---|
| Check or add token value | `tokens.md` |
| Build or use component | `components.md`, relevant `<component-folder>/<name>.md` |
| Map Figma frame to code | `components.md`, involved specs; refresh `figma-context/<node-id>.json` via MCP when available |
| Make drift decision | `drifts.md` (existing), spec of the involved component, `verify-queue.md` |
| Encounter unknown Figma name | `verify-queue.md` (possibly already known), otherwise add a new `[VERIFY]` item |

## Component selection

Two rules that work together (Hard rule #4 + atomic order):

**1. Consume existing — never regenerate.** For every Figma element: first search whether a matching code component exists (via `components.md`, a `src/components/` scan, or Code Connect). If yes: import and use. Never generate a new version — a deviating use is either a prop choice, drift, or a legitimate reason to extend the existing component.

**2. Pick the highest atomic level that fits.** When several valid code components could match: prefer Pages > Templates > Organisms > Molecules > Atoms. Do not combine loose atoms when a higher-order component already does the job.

Brad Frost atomic-design — five levels:

- **Atom** — indivisible (Button, Input, Icon, Badge)
- **Molecule** — composition of atoms with one shared purpose
- **Organism** — has its own state, scroll behavior, or keyboard handling
- **Template** — layout skeleton without content (AppShell, ErrorLayout, DashboardLayout)
- **Page** — concrete page instance with content (NotFoundPage, UserDashboardPage)

> **Organic adoption.** The skill does NOT force all five levels. `components.md` grows organically from what exists in code. A project with only `src/components/ui/` may have just Atoms/Molecules/Organisms — that's fine. A project with `src/templates/` and `src/pages/` (or `app/(routes)/*.tsx` componentized) gets the upper levels added. Categories with zero entries do not appear in the actual project doc.

When in doubt: pick the lower level. On complete absence of a matching component → `component-missing` drift, do not auto-generate (Hard rule #9).

## Drift — short and pragmatic

Mark drift in the spec where it belongs, not as a separate process. Three types:

- **`value-mismatch`** — Code rendered output ≠ Figma. Fix at call site.
- **`token-mismatch`** — Token value in `theme/tokens.ts` ≠ Figma. Fix in theme.
- **`component-missing`** — Figma element without a code component. Mark it; agent does not auto-generate, developer creates the component.

Format: one line per drift in the "Drift notes" section of the spec.

```
- <type> [Severity][Owner] — <file:line> <what differs>. Action: <what to do>.
```

### Severity — heuristic

Category level, not hardcoded thresholds. Concrete numeric thresholds (e.g. "5% lightness delta", "2px spacing delta") each project records itself in its own CLAUDE.md if desired.

| Severity | Trigger |
|---|---|
| **Critical** | System-wide impact (font family, primary color, base radius). Or: state mechanism differs fundamentally (overlay vs opacity shift). Or: code-API mismatch (variant missing where Figma has it as a type). |
| **Major** | Visually detectable on side-by-side comparison. Or: code-missing variant existing as Type enum in Figma. Or: figma-missing semantic alias used by code in multiple components. |
| **Minor** | Below visible threshold. Naming typos. Cosmetic difference without render impact. Tech-debt boundary (but tech debt itself is not drift — see drift test). |

### Drift test (apply to every drift candidate)

One question, always: **"Would MCP code generation from this Figma node produce a visually wrong result?"**

- **Yes** → drift, in `drifts.md` + spec.
- **No** → another bucket:
  - `verify-queue.md` — `[VERIFY]` items for the next live-MCP session (unverified masters, unlocated overrides, derived data without source check).
  - **Tech debt** — hardcoded-with-correct-value, tokenization candidates, dead code → not in mapping docs, belongs in code review or issue tracker.
  - **Mapping-doc fix** — `tokens.md` or `components.md` was incorrectly documented → fix directly in that doc, no drift row.

Drift is about visual/structural mapping. NOT about: text content/copy, hardcoded-with-same-value (tech debt), code bugs, auto-layout container properties without token binding, unverified masters (those go in `verify-queue.md`), or `figma-master-missing` for code-only abstractions (administrative, not drift).

## Method A1-A6

### A1. Inventory

Scan the existing codebase. Identify:
- Where tokens live (CSS variables, theme object, Tailwind config, mix)
- Which component folders exist (`src/components/ui/`, etc.)
- Which format styles use (CSS modules, Emotion, styled-components, etc.)
- **Styling stack as a fact** — which API the project uses for styling, exclusively. See "Styling stack" requirement below.
- **Atomic-design layers present in code** — does the project have `src/templates/` (Templates as components) or `src/pages/` / `app/(routes)/*.tsx` exporting page-components (Pages)? Document which of the five atomic-design levels exist. If only Atoms/Molecules/Organisms exist, `components.md` stays at three sections. If Templates/Pages also exist as code-components, they get their own sections in `components.md`. See "Component selection" above for the five-level taxonomy.

Give the user a short summary before continuing.

#### Styling stack — document as project fact

A1 must produce an explicit styling-stack section in `tokens.md` (or a new `styling-stack.md` if cleaner per project). Format example:

```markdown
## Project styling stack

- **API:** Emotion `styled()` + `css={}` only
- **Theme access:** `import theme from 'theme'` → `theme.X`
- **Not used:** className-direct, inline-styles, Tailwind, styled-components, CSS modules
```

Three rules:

1. **One API only.** Document which styling API the project uses exclusively. If the project mixes APIs (e.g., legacy CSS modules + new Emotion), document both and note which is canonical for new work.
2. **What is NOT used.** Explicit "not used" list prevents downstream code-emit (in implementation-skill TBD) from introducing parallel paradigms. Without this list, any LLM operating on the codebase can reasonably "add Tailwind for this one thing".
3. **Theme/token access pattern.** Document the canonical import — `import theme from 'theme'`, `import { tokens } from '@/lib/tokens'`, etc. — so emit knows the convention.

This is mapping-data (a fact about the codebase), not implementation-discipline. The future implementation-skill consumes this to enforce single-API styling at code-emit time. Mapping documents; implementation enforces.

### A2. Fill tokens.md (incrementally)

Document only tokens that the first component touches. Subsequent components extend the tables.

> **Watch out — two parallel scales.** Some Figma kits (especially Tailwind-mirror kits) have two parallel scales with overlapping prefix names: a **semantic scale** (`radius-md`, `radius-lg` for component design) next to a **utility scale** (`rounded-md`, `rounded-xl` as Tailwind class mirrors). Verify in `tokens.md` which scale a component actually uses. Document explicitly per category to prevent confusion.

One row per token with:
- Code path (how you use it in components)
- Figma name (as it exists in Figma as a variable) or `[VERIFY]` if unconfirmed
- Value
- Use (short explanation)

> **Optional: Auto-layout conventions section.** If the project has a consistent convention for translating Figma's auto-layout primitives (fill, hug, direction) to code expressions (`flex-1`, `w-fit`, `flex-col`), document it in `tokens.md § Auto-layout conventions`. Token values are already in the token tables; this section captures the *semantic-intent translation* (fill, hug, direction) that does not map to a single token. Skip when patterns are ad-hoc — forced documentation of inconsistency creates overhead. See template for format.

### A3. Document the first component

Choose one representative component together with the user. For that component:

1. Read the implementation (component file, styles file, types file)
2. Read the wrapper layer if present
3. Fill `<component-folder>/<name>.md` based on `templates/component-spec.md`
4. Add to `components.md` with Uses column

#### Anti-pattern: mixed-axis Type enum in Figma

Figma kits often collapse orthogonal axes into one `Type=` enum. Example Button with 14 values:
- `primary`, `secondary` → color axis
- `Size-small`, `Size-default`, `Size-large` → size axis
- `Rounded` → shape axis
- `loading` → state axis
- `with icon`, `with icon right` → composition axis (children order)
- `Button group` → composition axis (parent component)

In code these are typically 5+ separate axes (`variant` × `size` × `className` × `children-order` × `parent-wrapper`).

**Mapping strategy:**
1. Categorize each Figma `Type=` value by its axis (color / size / shape / state / composition).
2. Map per axis in the Variant mapping table:
   - color-Type → code `variant` prop
   - size-Type → code `size` prop
   - shape-Type → code className tweak
   - state-Type → code composition (e.g. `disabled` + child)
   - composition-Type → code parent wrapper or children order
3. Document the decomposition explicitly in `<component-folder>/<name>.md` so MCP code generation knows which Figma `Type=` value maps to which code prop.

### A4. Mapping to Figma

Per element: fetch node data via cache (refresh via MCP), read the code, and propose a mapping (Figma property → code token or code path). User confirms. On doubt: `[VERIFY]` in the Figma-name column, do not improvise.

> **Warning — variable scope.** `get_variable_defs(nodeId)` returns **only variables this specific node consumes**. Vars that exist file-level but are not used by this node do not come back. For `figma-missing` conclusions in `tokens.md`: query at least 3 component pages from different categories (buttons / cards / forms / feedback) before marking the gap definitively. A single-node pass produces false-positive `figma-missing` flags that later passes have to retract.

#### A4a. MCP fetch order for large/complex nodes

1. `get_design_context(nodeId)` — direct fetch.
2. **On timeout or "too complex" response: payload reduction first, before splitting.** MCP servers support different parameters — use what your server has:
   - `excludeScreenshot: true` — suppresses the screenshot render, often the most expensive step (available on fileKey-based MCP, not on all desktop-active MCPs)
   - `forceCode: true` — forces code output even on truncation risk (available on both common Figma MCPs)

   A retry with payload reduction is cheaper than splitting via metadata directly.
3. **On persistent truncation or timeout:** `get_metadata(nodeId)` for the child tree.
4. Identify the relevant child nodes from the metadata XML.
5. Loop through and `get_design_context(<childId>)` per relevant child (with payload reduction if needed); assemble the picture.

Do not improvise when the first fetch is incomplete — always go via payload reduction or metadata splitting before mapping further.

#### A4b. Mandatory comparison per hardcoded value

For every hardcoded value in code (padding, border-radius, height, etc.) compare directly with the Figma instance value from MCP output. Do not just note "code value + token" — explicitly apply the drift test (Hard rule #3): does it match the Figma rendered output? On difference: drift in spec + `drifts.md`, not only in re-validation. **Drift detection belongs to A4, not later.**

#### A4c. Literal strings are mapping too

Component-specific strings emitted in code — `aria-label`, `alt`, `placeholder`, `title` attributes — are mapping data, not behavior. Map them like tokens: code value + Figma source. Prevents MCP code generation from producing a generic `aria-label="Close"` instead of the existing `"Sluit venster"`. Not all a11y aspects are mapping (focus traps, keyboard navigation are behavior) — but these literal strings are.

#### A4d. Query state symbols separately from default symbols

For every variant with a `State=` enum in Figma (hover, focus, active, disabled): query the state symbol(s) **separately from** the default symbol.

Page-level `get_variable_defs(parent-frame)` aggregates vars over all child nodes — that hides which variant consumes which variable. Conclusions about state mechanism on aggregated data are structurally fragile: they lead to incorrect drifts or wrong hover/active mappings that subsequent passes have to correct.

Method:
1. `get_variable_defs(default-symbol-id)` — shows default tokens.
2. `get_variable_defs(state-symbol-id)` — shows whether the state uses a different color or mechanism.
3. Diff the results. Different vars in state = mechanism deviates (e.g. color shift to a different var instead of opacity reduction on the same var).

Document per variant in `<component-folder>/<name>.md` (Variant-mapping subsection) which state mechanism is active.

### A5. Finish Uses recursively (no separate permission ask)

After every component mapping: scan the Uses column of the just-mapped component. For every not-yet-mapped **internal** Use: continue mapping immediately — part of completing the original component, not a separate pass.

- Agent announces in advance: *"I'll map X now, then automatically also Y and Z (Uses of X)."*
- No separate permission ask per child component (exception to Hard rule #7)
- Still ask confirmation on changes to code/docs (Hard rule #7 still applies)

**Stop boundary:** external libraries (MUI, react-modal, framework components) are not mapped. They are mentioned in the spec under "Composition" or "Example", but get no spec of their own.

**Example:** if `ConfirmationModal` Uses = `Modal, Button, SecondaryButton`, all three get mapped in the same session. `Modal` in turn uses `react-modal` externally — that's where the chain stops.

When code is genuinely missing (component does not exist while Figma shows one): stop and mark as `component-missing` drift, ask what to do.

### A6. Validation checklist (closing per mapping pass)

At the end of every component mapping (before commit/sync) walk through these 8 checks explicitly. Drift test is a filter (what am I going to mark?); this checklist is positive (did I let nothing slip silently?).

| # | Check | Where validated |
|---|---|---|
| 1 | **Layout** — sizing, spacing, alignment match MCP output (within scope rules) | Mapping tables + drift notes |
| 2 | **Typography** — font-family, size, weight, line-height match Figma style | Mapping table under "Text" |
| 3 | **Colors** — exact match on Figma variable (Yellow/Y100, Blue/B30, etc.) or `[VERIFY]` | Mapping table under "Container/Color" |
| 4 | **States** — variants and states (hover/focus/active/disabled) mapped where Figma shows them | Variant-mapping subsection |
| 5 | **Assets** — SVG/icon/image sources reference existing project assets or MCP-localhost URL — no new imports, no placeholders | Mapping table "Icon-source / Asset" |
| 6 | **Literal strings** — `aria-label`, `alt`, `placeholder`, `title`, hardcoded labels in code are mapped (code value + source) | Mapping table "Text" or separate row "Aria-label" |
| 7 | **Token-verdict per row** — every code-value row in the mapping table has a verdict in column 3 (token-path / raw-token-available / raw-legitimate). No bare "hardcoded" entries. | Mapping tables |
| 8 | **Drift test passed** — candidate issues classified: drift, verify-queue, or discarded | `drifts.md` + `verify-queue.md` |

Tick off in the spec under "Drift notes": *"Spec last validated: [date] (A6 walked through)."*

## Lessons learned

Record when a rule **did not work** (correction needed) **and** when a rule **did work** (deliberately repeat). Strict format, max 5 lines per entry. New entries at the bottom.

```
[LESSON — YYYY-MM-DD] [type: correction | confirmation]
Situation: <what happened, 1 line>
What worked (or did not): <observation, 1-2 lines>
Proposal: <change rule or keep, 1 line>
```

---

[LESSON — 2026-05-05] [confirmation]
Situation: Live MCP test on modal frame `16599:2645` after strict drift test + mapping-only specs.
What worked: Drift test as filter worked: 7 candidates in, 5 real drifts, 0 noise. 2 new Button drifts found (padding-x 24 vs 20, radius 40 vs 44).
Proposal: Keep drift test + mapping-only specs — right regime for MCP code-generation quality.

[LESSON — 2026-05-05] [correction]
Situation: First Button mapping noted only code values ("16px 24px | theme.spacing.lg + xl") without Figma comparison. Drifts found only in re-validation.
What did not work: Spec tables were code documentation, not mapping comparison. Drift detection hung on re-validation instead of the first pass.
Proposal: Make A4 mandatory: per hardcoded value, compare directly with Figma instance. Mark drift on first pass, not later. (Implemented in A4b.)

[LESSON — 2026-05-05] [correction]
Situation: Agent interpreted Figma's auto-paste "Implement this design from Figma." (from *Copy Link* Dev mode) as a user instruction and made token + button.tsx changes, followed by revert.
What did not work: Boilerplate text was confused with explicit dev task. Hard Rule #2 inadvertently reasoned away without the user asking.
Proposal: Make Hard Rule #2 explicitly split out what does/does not count as code-update trigger. (Implemented in v2.4.)

[LESSON — 2026-05-05] [confirmation]
Situation: get_design_context on Button size symbols in a complex Figma kit returned MCP timeout. First pass stranded three items in verify-queue.
What worked: Retry with payload-reduction parameter (`excludeScreenshot: true` on fileKey-based MCP) succeeded directly on all 3 nodes. Cheaper than metadata split.
Proposal: Extend A4a — payload reduction as first fallback for timeouts, before metadata split. (Implemented in v2.5.)

[LESSON — 2026-05-05] [correction]
Situation: Pass 1 (Accordion only) marked 14 tokens as figma-missing. Pass 2 on Button page proved that 6 of those do exist in Figma — just not on Accordion. One large correction needed in tokens.md + drifts.md.
What did not work: Single-node `get_variable_defs` is scope-limited to what that node consumes. False-positive `figma-missing` flags arise automatically from a limited query set.
Proposal: A4 warning — query at least 3 component pages from different categories before declaring `figma-missing` definitive. (Implemented in v2.6.)

[LESSON — 2026-05-05] [correction]
Situation: Pass 2 concluded primary-hover used a white/12 overlay based on Button-page-level vars. Pass 3 (per-symbol query on 73:3668) showed that primary-hover uses chart-1 + opacity:0.9 — no white/12.
What did not work: Page-level `get_variable_defs` aggregates vars; identity per variant/state is lost. Conclusions about hover mechanism on aggregated data are structurally fragile.
Proposal: Add A4d — query State=hover/focus/active symbols separately for accurate mapping. (Implemented in v2.6.)

[LESSON — 2026-05-05] [confirmation]
Situation: Live MCP tests confirm asset URLs differ per MCP server: `localhost:3845/...` (desktop-active) vs `figma.com/api/mcp/asset/...` (fileKey-based, 7-day TTL). Plus: fileKey-MCP supports `excludeScreenshot`, desktop-MCP does not.
What worked: Documenting both MCPs explicitly with capability differences in the skill helps agents pick which tool fits batch vs eyes-on work.
Proposal: Make MCP-server table + asset-URL dual-format explicit in Source mechanism. (Implemented in v2.6.)

[LESSON — 2026-05-06] [confirmation]
Situation: Mapping pass on a third-party Figma kit with Dutch SKILL.md/README; LLM had to translate concepts internally before reasoning, sometimes losing precision on technical terms (e.g. "bron-verdeling" ↔ "source-of-truth allocation").
What worked: Translating the entire skill (SKILL.md, README, templates) to English aligned terminology with library docs (Figma, Tailwind, React) and reduced internal translation cost. Dutch nuance preserved in core principles ("drift = decision point, not debt").
Proposal: Skill written in English; project-side mapping outputs may stay in any language the team prefers. (Implemented in v2.7.)

[LESSON — 2026-05-08] [correction]
Situation: Developer test on a 404 page in a third-party Figma kit project showed mixed styling APIs in emitted code — Emotion `styled` AND className-direct on the same element. Two styling locations for the same element.
What did not work: Skill identified the styling-stack in A1 inventory but did not bind it as a fact for downstream consumption. Mapping had no place documenting "this project uses Emotion only — no className, no inline".
Proposal: A1 produces explicit "Project styling stack" section in tokens.md as mapping-fact (API, theme access, not-used list). Future implementation-skill consumes for emit-time enforcement. Mapping documents; implementation enforces. (Implemented in v2.8.)

[LESSON — 2026-05-08] [correction]
Situation: Same developer test — emitted code introduced raw color values (`#fafafa` etc.) where matching tokens existed in `theme/tokens.ts`. Mapping had documented some values as bare "hardcoded" without verdict — leaving downstream emit no signal that a token was available.
What did not work: Mapping table column 3 allowed prose ("hardcoded") without a verdict. No structured way to flag "code uses raw, matching token exists" — implementation-skill can't enforce what mapping doesn't capture.
Proposal: Hard rule #11 — every code-value row gets one of three verdicts (token-path / raw-token-available / raw-legitimate). A6 validation enforces. Mapping captures fact; implementation enforces emit-time. (Implemented in v2.9.)

[LESSON — 2026-05-08] [correction]
Situation: Earlier skill referenced atomic-design but only documented three levels (Atoms/Molecules/Organisms). Brad Frost's framework has five — Templates and Pages — and many projects (Next.js apps, dashboard apps) have these as code-components yet no place to document them.
What did not work: Three-level taxonomy forced page-as-organism collapsing. Pages with their own tokens/styling fell out of mapping scope when they should be in.
Proposal: A1 inventories all five levels organically — only levels with actual code-components surface in `components.md`. No forced empty categories. Pages/Templates as components get their own sections. (Implemented in v2.10.)

[LESSON — 2026-05-08] [correction]
Situation: Responsiveness loss on a 404 page in developer testing — emit produced absolute pixel-width where Figma was auto-layout fill. Token mapping (gap-8 → 8px) was in tokens.md, but no place documented "Figma fill ↔ flex-1" — semantic-intent translation had no home.
What did not work: Token mapping captures values but not auto-layout primitives (fill, hug, direction). Implementation-skill cannot consume what mapping does not document.
Proposal: Optional Auto-layout conventions section in tokens.md — only fill if project has consistent convention. Captures semantic-intent translation alongside token values. Implementation-skill consumes for emit-time auto-layout-to-CSS. (Implemented in v2.11.)

[LESSON — 2026-05-08] [correction]
Situation: Audit of Hard rule #4 against the verb-test (CLAUDE rule #6) showed three of three verbs were emit-flavor (consume, search...first, never generate). Rule had implementation-discipline phrasing while sitting in mapping-rules. Asymmetric to Hard rule #11 (token-verdict) which is fact-capture.
What did not work: Loose phrasing crossed scope-line. The discipline IS mapping (don't shortcut by inventing components instead of linking existing), but verbs read as emit-time refusal.
Proposal: Reframe with mapping-verbs (link, mark, document); add explicit "Mapping vs implementation" comparison table to SKILL.md; mirror to README/CLAUDE for scope-discipline anchor. (Implemented in v3.0.)

[LESSON — 2026-05-08] [correction]
Situation: Developer test feedback plus internal review showed that the skill name "figma-to-code" suggested code-generation responsibility — developers expected emit-time discipline. Skill scope is purely mapping; mismatch caused expectation-failures.
What did not work: Naming implied a broader scope than the skill delivers. "figma-to-code" reads as full pipeline; reality is one half (mapping).
Proposal: Rename to "figma-to-code-mapping" — explicit half-of-pipeline name. Reserves "figma-to-code-implement" namespace for the future emit-skill. Breaking change for projects with existing CLAUDE.md references; migration documented in README. (Implemented in v3.0.)

## References

- `templates/tokens.md` — empty tokens template
- `templates/components.md` — empty components-index template
- `templates/component-spec.md` — template for a single component spec
- `templates/drifts.md` — empty drift-aggregator template
- `templates/verify-queue.md` — empty `[VERIFY]`-queue template
- `templates/claude-md-snippet.md` — markdown to paste into project CLAUDE.md
- `templates/example-confirmation-modal/` — filled example
