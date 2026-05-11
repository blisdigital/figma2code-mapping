# Lessons learned

Record what worked and what did not during the skill's evolution. Each entry teaches the skill about itself — confirmations to deliberately repeat, corrections to fix.

> **Format and discipline:** see `CLAUDE.md § Edit rules #3` and `§ Writing lessons-learned`. Strict 5-line format, append-only, one rule per entry.

---

[LESSON — 2026-05-05] [confirmation]
Situation: Live MCP test on modal frame `16599:2645` after strict drift test + mapping-only specs.
What worked: Drift test as filter worked: 7 candidates in, 5 real drifts, 0 noise. 2 new Button drifts found (padding-x 24 vs 20, radius 40 vs 44).
Proposal: Keep drift test + mapping-only specs — right regime for MCP code-generation quality.

[LESSON — 2026-05-05] [correction]
Situation: First Button mapping noted only code values ("16px 24px | theme.spacing.lg + xl") without Figma comparison. Drifts found only in re-validation.
What did not work: Spec tables were code documentation, not mapping comparison. Drift detection hung on re-validation instead of the first pass.
Proposal: Make A4 mandatory: per hardcoded value, compare directly with Figma instance. Mark drift on first pass, not later.

[LESSON — 2026-05-05] [correction]
Situation: Agent interpreted Figma's auto-paste "Implement this design from Figma." (from *Copy Link* Dev mode) as a user instruction and made token + button.tsx changes, followed by revert.
What did not work: Boilerplate text was confused with explicit dev task. Hard Rule #2 inadvertently reasoned away without the user asking.
Proposal: Make Hard Rule #2 explicitly split out what does/does not count as code-update trigger.

[LESSON — 2026-05-05] [confirmation]
Situation: get_design_context on Button size symbols in a complex Figma kit returned MCP timeout. First pass stranded three items in verify-queue.
What worked: Retry with payload-reduction parameter (`excludeScreenshot: true` on fileKey-based MCP) succeeded directly on all 3 nodes. Cheaper than metadata split.
Proposal: Extend A4a — payload reduction as first fallback for timeouts, before metadata split.

[LESSON — 2026-05-05] [correction]
Situation: Pass 1 (Accordion only) marked 14 tokens as figma-missing. Pass 2 on Button page proved that 6 of those do exist in Figma — just not on Accordion. One large correction needed in tokens.md + drifts.md.
What did not work: Single-node `get_variable_defs` is scope-limited to what that node consumes. False-positive `figma-missing` flags arise automatically from a limited query set.
Proposal: A4 warning — query at least 3 component pages from different categories before declaring `figma-missing` definitive.

[LESSON — 2026-05-05] [correction]
Situation: Pass 2 concluded primary-hover used a white/12 overlay based on Button-page-level vars. Pass 3 (per-symbol query on 73:3668) showed that primary-hover uses chart-1 + opacity:0.9 — no white/12.
What did not work: Page-level `get_variable_defs` aggregates vars; identity per variant/state is lost. Conclusions about hover mechanism on aggregated data are structurally fragile.
Proposal: Add A4d — query State=hover/focus/active symbols separately for accurate mapping.

[LESSON — 2026-05-05] [confirmation]
Situation: Live MCP tests confirm asset URLs differ per MCP server: `localhost:3845/...` (desktop-active) vs `figma.com/api/mcp/asset/...` (fileKey-based, 7-day TTL). Plus: fileKey-MCP supports `excludeScreenshot`, desktop-MCP does not.
What worked: Documenting both MCPs explicitly with capability differences in the skill helps agents pick which tool fits batch vs eyes-on work.
Proposal: Make MCP-server table + asset-URL dual-format explicit in Source mechanism.

[LESSON — 2026-05-06] [confirmation]
Situation: Mapping pass on a third-party Figma kit with Dutch SKILL.md/README; LLM had to translate concepts internally before reasoning, sometimes losing precision on technical terms (e.g. "bron-verdeling" ↔ "source-of-truth allocation").
What worked: Translating the entire skill (SKILL.md, README, templates) to English aligned terminology with library docs (Figma, Tailwind, React) and reduced internal translation cost. Dutch nuance preserved in core principles ("drift = decision point, not debt").
Proposal: Skill written in English; project-side mapping outputs may stay in any language the team prefers.

[LESSON — 2026-05-08] [correction]
Situation: Developer test on a 404 page in a third-party Figma kit project showed mixed styling APIs in emitted code — Emotion `styled` AND className-direct on the same element. Two styling locations for the same element.
What did not work: Skill identified the styling-stack in A1 inventory but did not bind it as a fact for downstream consumption. Mapping had no place documenting "this project uses Emotion only — no className, no inline".
Proposal: A1 produces explicit "Project styling stack" section in tokens.md as mapping-fact (API, theme access, not-used list). Future implementation-skill consumes for emit-time enforcement. Mapping documents; implementation enforces.

[LESSON — 2026-05-08] [correction]
Situation: Same developer test — emitted code introduced raw color values (`#fafafa` etc.) where matching tokens existed in `theme/tokens.ts`. Mapping had documented some values as bare "hardcoded" without verdict — leaving downstream emit no signal that a token was available.
What did not work: Mapping table column 3 allowed prose ("hardcoded") without a verdict. No structured way to flag "code uses raw, matching token exists" — implementation-skill can't enforce what mapping doesn't capture.
Proposal: Hard rule #11 — every code-value row gets one of three verdicts (token-path / raw-token-available / raw-legitimate). A6 validation enforces. Mapping captures fact; implementation enforces emit-time.

[LESSON — 2026-05-08] [correction]
Situation: Earlier skill referenced atomic-design but only documented three levels (Atoms/Molecules/Organisms). Brad Frost's framework has five — Templates and Pages — and many projects (Next.js apps, dashboard apps) have these as code-components yet no place to document them.
What did not work: Three-level taxonomy forced page-as-organism collapsing. Pages with their own tokens/styling fell out of mapping scope when they should be in.
Proposal: A1 inventories all five levels organically — only levels with actual code-components surface in `components.md`. No forced empty categories. Pages/Templates as components get their own sections.

[LESSON — 2026-05-08] [correction]
Situation: Responsiveness loss on a 404 page in developer testing — emit produced absolute pixel-width where Figma was auto-layout fill. Token mapping (gap-8 → 8px) was in tokens.md, but no place documented "Figma fill ↔ flex-1" — semantic-intent translation had no home.
What did not work: Token mapping captures values but not auto-layout primitives (fill, hug, direction). Implementation-skill cannot consume what mapping does not document.
Proposal: Optional Auto-layout conventions section in tokens.md — only fill if project has consistent convention. Captures semantic-intent translation alongside token values. Implementation-skill consumes for emit-time auto-layout-to-CSS.

[LESSON — 2026-05-08] [correction]
Situation: Audit of Hard rule #4 against the verb-test (CLAUDE rule #6) showed three of three verbs were emit-flavor (consume, search...first, never generate). Rule had implementation-discipline phrasing while sitting in mapping-rules. Asymmetric to Hard rule #11 (token-verdict) which is fact-capture.
What did not work: Loose phrasing crossed scope-line. The discipline IS mapping (don't shortcut by inventing components instead of linking existing), but verbs read as emit-time refusal.
Proposal: Reframe with mapping-verbs (link, mark, document); add explicit "Mapping vs implementation" comparison table to SKILL.md; mirror to README/CLAUDE for scope-discipline anchor.

[LESSON — 2026-05-08] [correction]
Situation: Developer test feedback plus internal review showed that the skill name "figma-to-code" suggested code-generation responsibility — developers expected emit-time discipline. Skill scope is purely mapping; mismatch caused expectation-failures.
What did not work: Naming implied a broader scope than the skill delivers. "figma-to-code" reads as full pipeline; reality is one half (mapping).
Proposal: Rename to "figma-to-code-mapping" — explicit half-of-pipeline name. Reserves "figma-to-code-implement" namespace for the future emit-skill. Breaking change for projects with existing CLAUDE.md references; migration documented in README.

[LESSON — 2026-05-08] [confirmation]
Situation: SKILL.md grew to 504 lines after 13+ refinements; Lessons learned section alone was ~65 lines and re-loaded on every application pass although only the most recent entries are tactically relevant.
What worked: Moving lessons-learned to a separate `LESSONS.md` keeps the historical record while reducing per-application context load. Format spec lives in CLAUDE.md (edit-meta info).
Proposal: New lessons append to `LESSONS.md`, not to SKILL.md. CLAUDE.md `§ Writing lessons-learned` instructs where.

[LESSON — 2026-05-11] [correction]
Situation: Hard rule #4 forced "every Figma element MUST be linked to an existing code component". Atomic-design bias from v2.10 (5 levels) — implicit assumption that every frame is an atom/molecule/organism/template/page. Reality: ~60% of page frames are element-frames (no Figma master, no code-component equivalent — illustration wrappers, hero text, layout containers). Old rule produced false `component-missing` drifts and spec-file bloat for these.
What did not work: Skill conflated "frame without master that should be a component" with "frame without master that legitimately is not a component". Element-frames don't need component-spec — token-mapping suffices.
Proposal: Hard rule #4 classification table with four frame types (component-instance / frame↔code-component / element-frame / component-missing drift) + A4-classify step. Vision-statement: messy Figma is upstream design-ops, not mapping's job to outsmart. Components.md template note excluding element-frames.

[LESSON — 2026-05-11] [correction]
Situation: First-pass v3.2 plan assigned the "Frame ↔ code-component" detection to mapping-time as "deliberate human/agent decision", but did not specify mechanism. Implement-skill design (sister repo) proposed a graduated fingerprint detection (token-cluster + naming + structural pattern, ≥2 signals → propose, user gates) and pointed out this is detection logic, not documentation — belongs in implement, not mapping.
What did not work: Mapping-time aggressive detection would force "is this a hidden Button?" question on every illustration wrapper — most frames in real pages. Mapping should keep element-frame as safe default and accept feedback when implement finds a hygiene gap.
Proposal: Hard rule #4 case 4 (Frame ↔ code-component) is detected by implement-skill at emit-time via fingerprint with user confirmation. Result flows back as `verify-queue.md` entry "frame-should-be-instance". Mapping reads that entry next pass and promotes the link in `components.md` with `figma-master-missing` note. Bidirectional ground-truth: mapping canonical, implement feeds back discoveries. Verify-queue template documents entry sources.

[LESSON — 2026-05-11] [correction]
Situation: Previous lesson proposed detection wholly in implement-skill, making mapping wait for fallback. User flagged this as wrong inversion: mapping should detect primary, implement is fallback for missed cases.
What did not work: Pushing detection out of mapping leaves the skill structurally incomplete — frame-shaped-Button without master would never be caught at mapping-time. Mapping has all the data it needs (var-enumeration, frame names, metadata XML) to detect at A4-classify with structured signals.
Proposal: A4-classify gains data-signal fingerprint (naming string-match + ≥80% token-cluster overlap + structural archetype). ≥2 signals → propose-and-user-gate. Strict vibe-guard: no visual similarity, no pixel-matching. Implement-skill keeps its own fingerprint as fallback for low-signal frames mapping missed; result flows back via verify-queue.md. Mapping primary, implement safety net.

[LESSON — 2026-05-11] [correction]
Situation: Reviewing v3.2 fingerprint design (Option A) against the skill's vision ("messy Figma is upstream design-ops, not mapping's job to outsmart") showed mismatch — a systematic scan with score thresholds is exactly the kind of active hunting the vision rules out. Also created a premature dependency on a sister skill (`figma-to-code-implement`) that does not yet exist.
What did not work: Score-threshold fingerprint reads as enforcement, not mapping. It also pushes the agent toward AI-guessing land via formalized scoring — even with data-only signals, low-signal noise grows when scanning every frame on every pass.
Proposal: Option B — replace systematic scan with incidental notice. Agent surfaces an alignment only when name + tokens + structure obviously align with an existing component during normal A4 work. No score-table, no active hunt. User always gates (structure ≠ intent; Hard rule #7 consistency; low friction because strong alignments are rare). Drop verify-queue's implement-skill fallback row — premature dependency.

[LESSON — 2026-05-11] [confirmation]
Situation: After v3.2 closed PRs #6 (implement-handoff doc) and #7 (cache-schema mini-PR) under "wait for implement ship" discipline. Sister skill `figma-to-code-implement` v0.1 then shipped, unblocking the deferred items.
What worked: Holding the coordinated edits for the actual ship-moment kept the skill repo clean and the lessons honest — no aspirational doc, no premature schema, no broken cross-links to a non-existent skill. The implement skill itself stayed independently shippable; the mapping-side coordination landed as one cohesive PR (v3.3) when both sides actually existed.
Proposal: For future sister-skill coordination — never land in mapping while sister is design-only. Bundle coordination edits and apply once the dependency is real. The "wait" is not delay; it is preventing rot.

[LESSON — 2026-05-11] [correction]
Situation: Pelle's run + audits 1+4 revealed three concrete hardening gaps: (a) `figma-context/` ended up public because setup's gitignore step was documented but not enforced; (b) styling-stack section was optional in wording, causing dual-styling regression (className in Emotion codebase); (c) Figma image assets fell through to placeholder text in emit because no per-project asset contract existed.
What did not work: Documentation-only enforcement (security + styling-stack) is silent fallback by design — agents skip on partial permissions. Asset-handling Hard rule #8 said "no placeholders" but had no structured registry to anchor what should be where, so emit had no choice but to placeholder.
Proposal: v3.5 Tranche 1 — three hardening fixes. Setup fails hard on .gitignore write-block; styling-stack `[REQUIRED]` marker + halt before A2; new A4e asset-detection step writing to `tokens.md § Asset-mapping registry` (registry-only, no download — implement materialises). A6 gets two pre-component-check setup-integrity checks (0a, 0b). Mapping documents; implement enforces — but mapping is now strict about what it documents.

[LESSON — 2026-05-11] [correction]
Situation: Pelle citeerde dat de bare MCP-output verantwoordelijkheid voor responsive intent in code-comments dumpt ("hoe veel bij hoeveel achtergrond moest zijn"). Mapping had geen plek voor breakpoint × what-changes; agents lieten het in comments belanden of vroegen het per emit opnieuw.
What did not work: Component-spec template had geen responsive-section. Token-tabel dekt waarden, niet *intent-per-breakpoint*. Responsive variants in Figma (Size=mobile/desktop, Fill constraints) waren mapping-data zonder huis.
Proposal: v3.5 Tranche 2 — new `## Responsive behavior` section in component-spec template, optional (skip-entirely-if-static). New A4f step: conditional documentatie alleen als Figma constraints of responsive variants heeft. Implement-skill consumeert sectie voor unit-keuze (rem vs px) en layout primitives.

[LESSON — 2026-05-11] [correction]
Situation: drifts.md werkte als archief — niemand opende het zonder reden, beslis-stap gebeurde nergens automatisch. Mapping detecteerde drift, schreef droog naar markdown, daarna stopte het. Vision-mechanism #3 ("drift as decision point, not silently resolved") was incompleet in implementatie.
What did not work: zonder proactieve surfacing en zonder status-tracking is drifts.md "informatie die niemand leest". Detection en logging werkten al; surfacing-aan-beslisser en decision-routing ontbraken.
Proposal: v3.5 Tranche 3 — proactieve post-A6 drift-review prompt (geen aparte slash-command). Als drifts.md ≥1 OPEN entry heeft na A6 checks → prompt user `[y/N]` voor walk-through, sorted by severity, 3 opties per drift (revert-figma / accept / update-code). Drifts.md krijgt Action-kolom met enum (OPEN / ACCEPTED / IGNORED / SCHEDULED / RESOLVED). Status-log append automatisch. Mapping-side drift-loop gesloten; implement-side blijft eigen verantwoordelijkheid van sister skill.
