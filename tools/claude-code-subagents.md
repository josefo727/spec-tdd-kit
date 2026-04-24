# Claude Code — Subagents

Subagents are specialized agents spawned via the `Agent` tool with a `subagent_type`. They run in isolation and return a single result. The kit uses them deliberately — not for everything.

## When to use a subagent

- **Context hygiene.** A task will pull many files or large search results; spawning a subagent keeps the main context clean.
- **Independent parallelism.** Two research tasks that do not depend on each other.
- **Specialization.** A task aligns with an existing specialized agent (Plan, Explore, stack-specific builders).

## When NOT to use a subagent

- The task is a single tool call (`Read`, `Bash`, `Grep`).
- The result must be visible to the user in real time.
- The task requires tight interactive dialog (use `AskUserQuestion` instead).

## Subagents the kit maps to phases

| Phase | Subagent | Purpose |
|---|---|---|
| `specify` | (none by default) | The spec is user-facing dialog; no subagent |
| `clarify` | (none) | `AskUserQuestion` only |
| `plan` | `Plan` | Draft implementation plan with tradeoffs |
| `plan` (brownfield intake) | `Explore` | Map the repo |
| `implement` (Laravel) | `laravel-feature-builder` | Scaffold feature following project patterns |
| `implement` (Vue) | `vue-developer` | Components, stores, router |
| `implement` (generic) | `general-purpose` | Ad-hoc tasks |
| `implement` (refactor) | `laravel-simplifier` (for PHP) | Refinement pass during F |
| `verify` (debug) | `laravel-debugger` (for PHP) | Stack-aware triage |
| `verify` (security) | — | Use `security-review` skill instead |
| `docker setup` | `docker-specialist` | Hermetic environments |

## Isolation

Use `isolation: "worktree"` when the subagent will make changes you want to review before merging into the main workspace:

```
Agent({
  description: "Implement T042 in a worktree",
  subagent_type: "laravel-feature-builder",
  isolation: "worktree",
  prompt: "…"
})
```

The worktree is cleaned up automatically if no changes are made; otherwise the path and branch are returned.

## Prompting subagents well

- Briefing, not command: what the task is, what has been tried, why it matters.
- Tell the subagent to write code or just to research.
- Ask for a bounded-length report ("under 200 words") when you want the answer without the raw output.
- Never delegate synthesis ("based on your findings, implement it") — that belongs to the caller.

Example good prompt:

```
Audit `app/Domain/Umbrella` for coverage of spec §4.1.
Input: .specs/001-umbrella/spec.md, tests/Unit/Umbrella/*.
Return: a punch list — criteria covered vs. missing. Under 150 words.
Do not write code.
```

## Parallel subagents

Send multiple `Agent` tool calls in the same message to run them concurrently. Use for independent research, not for implementations that touch the same files.

## Cross-references

- Skills: [`./claude-code-skills.md`](./claude-code-skills.md)
- Implement phase: [`../phases/05-implement.md`](../phases/05-implement.md)
- Greenfield plan step: [`../workflows/greenfield.md`](../workflows/greenfield.md)
