# figma2code — repo context for Claude

When Claude works in this repo, the following meta rules apply (separate from what is in `SKILL.md` — those are rules for *applying* the skill in a project; these are for *editing* the skill itself).

## Context

This repo is the **source of truth** for the figma-to-code-mapping skill (renamed from `figma-to-code` in v3.0 to clarify the half-of-pipeline scope). The skill is installed by developers via a symlink:

```
~/.claude/skills/figma-to-code-mapping → ~/Github/figma2code
```

Every change to `SKILL.md` or `templates/` becomes active after `git pull` in every project that uses the symlink. **Changes propagate broadly** — there is no "test in one repo first".

## Edit rules

1. **Branch + PR required.** Direct push to `main` is blocked by hooks. Always work on a feature branch with a descriptive name (`skill-<what>`, e.g. `skill-asset-handling-validation-boundary`).
2. **One type of change per PR.** Split feature additions, readability restructures, and template changes into separate PRs. Mixing makes review and rollback hard.
3. **Lessons-learned go in `LESSONS.md`, not `SKILL.md`.** Max 5 lines per entry, exact fields: `Situation`, `What worked (or did not)`, `Proposal`. Date + type (correction | confirmation) in the header. Append at the bottom; never edit older entries (history matters). No free text around the entries.
4. **Bump version in frontmatter** on every significant change to `SKILL.md`. Patch (2.0 → 2.1) for refinements, minor (2.x → 3.0) on behavior changes that affect existing mappings.
5. **Do not write templates into projects.** Changes to `templates/` only land in *new* projects via `/figma-to-code-mapping setup`. Existing mapping docs in projects stay untouched — by design, to avoid migration pain.
6. **Hold the mapping/implementation line.** When considering a new rule, ask: does the skill *document* this (mapping) or *enforce* this at code-emit time (implementation)? Verbs like "consume", "refuse", "translate", "search-and-adopt", "apply" usually signal implementation territory. Mapping verbs are "document", "detect", "inventory", "mark", "link", "capture". On uncertainty: park as note for the future `figma-to-code-implement` skill TBD.

## Skill vs project mapping

- `SKILL.md` = method + rules for *applying* the skill in a project (loaded in every project)
- `CLAUDE.md` = edit rules for *this repo* (loaded only when Claude works in this repo)
- `templates/` = starting point for projects installing the skill
- `README.md` = developer-facing setup guide on GitHub

When in doubt whether something belongs in `SKILL.md` or `CLAUDE.md`: **does it concern making a mapping in a project? → SKILL.md. Does it concern maintaining this repo? → CLAUDE.md.**

## Writing lessons-learned

For every significant lesson (both corrections and confirmations) one entry **at the bottom of `LESSONS.md`** (not in SKILL.md — keeps the application-time skill light):

```
[LESSON — YYYY-MM-DD] [type: correction | confirmation]
Situation: <what happened, 1 line>
What worked (or did not): <observation, 1-2 lines>
Proposal: <change rule or keep, 1 line>
```

No longer. No vaguer. On overflow: split into two entries or the lesson is not sharply enough formulated.

When a lesson's proposal becomes a Hard rule or method change, mark the rule itself in SKILL.md but do not add the version-implemented suffix to the lesson — the lesson stays factual about the situation, the rule is the resolution.

## Reference

- The symlink is created by the end user (see `README.md`); for debug sessions you can run `ls -la ~/.claude/skills/figma-to-code-mapping` to verify the symlink exists.
- Test changes: `git pull` in your own `~/Github/figma2code/`, then trigger Claude in a test project with `/figma-to-code-mapping map <component>` on a new component.
