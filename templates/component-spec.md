# [ComponentName]

[One-sentence technical description: what is the component, which library/wrapper it builds on.]

**Figma:** `[node-id]` ([link](https://www.figma.com/...)) — master | frame | no master

**Location in codebase:** `src/components/...`

**Type:** atom | molecule | organism

## Composition

| Component | Location |
|---|---|
| `[Name]` | `src/components/...` or `[external]` |

**Uses:** [Comma-separated list of direct component imports — internal only. Atoms have `—` here.]

## Example (optional)

```tsx
// One canonical use, max 5 lines. Omit if trivial.
```

## Props

[Compact — only project-specific props. Pass-through props (e.g. MUI) omit unless default differs.]

| Prop | Type | Default | Description |
|---|---|---|---|
| `[name]` | `[type]` | `[default]` | [short description — what it sets, no behavior] |

## Mapping: Figma → Code

[Per sub-element one short paragraph (max 3 lines) + table. No edge cases, no behavior, no "when to use".]

### [Sub-element name]

[Short mapping context — where in code, which tokens. Max 3 lines.]

| Property | Code value | Token / Source |
|---|---|---|
| [...] | [...] | [...] |

> **Token-verdict mandatory (Hard rule #11).** Every row's third column has one of three verdicts:
> - **Token-path** (e.g., `theme.neutral.N100`) — code uses token correctly
> - **`(raw, token available: <path>)`** — code uses raw value but matching token exists; flag for future implementation-skill
> - **`(raw, legitimate — no matching token)`** — no token exists for this value (e.g., `margin: 0` reset)
>
> No bare "hardcoded" entries. Mapping captures the fact; implementation enforces.

### Variant mapping (only when component has variants or states)

[Disambiguation for MCP: which Figma variant matches which code result.]

| Figma variant / state | Code component | Code props |
|---|---|---|
| `[e.g. primary]` | `[e.g. Button]` | `[e.g. variant="contained" color="highlight"]` |

## Drift notes

[Optional — only fill when there is drift that passes the drift test (would MCP code generation from Figma produce a visually wrong result? Yes → drift). `[VERIFY]` items, hardcoded-with-correct-value, and code-only abstraction administration do NOT belong here — those go to `verify-queue.md` or nowhere. One line per drift.]

> **Spec last validated:** [date]. Code files in sync (hash `[...]`). [Optional: cache status MCP.]

- [drift type] [Severity][Owner] — [file:line] [what differs]. Action: [what to do].
