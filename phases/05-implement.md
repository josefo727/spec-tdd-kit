# Phase 05 — Implement

## Purpose

Execute the tasks under a strict TDD cycle. This is where the fusion pays: every line of production code exists to satisfy a failing test that traces to a spec criterion.

## Flow per task

For each `T{NNN}` in `tasks.md`, in order (parallel only when `[P]` and files are disjoint):

```
┌──────── RED ─────────┐   ┌──────── GREEN ────────┐   ┌─────── REFACTOR ────────┐
│ write failing test   │ → │ minimal passing code  │ → │ clean while all green   │
│ run: observe failure │   │ run: all tests green  │   │ run: all tests green    │
│ commit: test: T… red │   │ commit: feat: T… green│   │ commit: refactor: T…    │
└──────────────────────┘   └───────────────────────┘   └─────────────────────────┘
```

### R — Red

- Write **one** test expressing one DoD bullet.
- Run only that test file (or the closest unit) to confirm the failure.
- Record the failure output; it goes into the commit body.
- Rules:
  - The test must fail because the feature is absent/incorrect, not because of scaffolding (unknown imports, missing stubs). Scaffold first, then assert.
  - One Red commit per task is the minimum; multiple Red commits (one per DoD bullet) are encouraged for non-trivial tasks.
- Slash command: `/tdd-red`. Captures output and drafts the commit.

### G — Green

- Implement the **smallest** code that makes the test pass.
- Run the **entire** test suite (unit + contract + integration levels touched). All tests must be green.
- If a previously green test broke, you overstepped. Narrow the change or decompose the task.
- Slash command: `/tdd-green`.

### F — Refactor

- With all tests green, improve:
  - names,
  - duplication,
  - cohesion/coupling,
  - readability,
  - coverage of edge cases (add tests before refactoring if new edges surface).
- Run the full suite after every meaningful change.
- If nothing needs refactoring, explicitly record `F: skipped — no smell detected` on the task entry. Persistent skipping across tasks is a smell; audit in `verify`.
- Slash command: `/tdd-refactor`.

## Commit conventions

Three separate commits per beat when practical. Examples:

```
test: T042 red — threshold=30 returns needsUmbrella=true at probability=30

Observed failure:
  tests/logic/umbrella.test.ts
  ● shouldTakeUmbrella › threshold at 30 triggers at probability=30
  ReferenceError: shouldTakeUmbrella is not defined

Refs: spec §4.1
```

```
feat: T042 green — implement shouldTakeUmbrella(threshold, data)
```

```
refactor: T042 — extract predicate, rename rainyHours
```

Squashing into one commit per task is acceptable when the team prefers linear history, **only if the squashed commit body preserves the R/G/F narrative**.

## Parallel execution

For `[P]` tasks:

- Prefer different branches or worktrees for each runner.
- Use `Agent` with `isolation: "worktree"` for subagent-driven parallel work ([`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md)).
- Merge order follows the task ID order unless an explicit dependency is noted.

## Stopping conditions

Stop the current task and surface to the user when:

- Red cannot be made to fail for the right reason after 3 attempts.
- Green requires touching files outside the task's `files` list without a good reason.
- A test unrelated to the task starts failing during the cycle.
- The plan appears wrong (contract mismatch, missing dependency).
- The constitution blocks a necessary step.

In all cases, do not "muscle through". Ask.

## Coverage and quality guardrails

- Linter clean after G and after F.
- Type checker clean after G and after F.
- No new warnings introduced. Existing warnings are acceptable unless the constitution forbids.
- No `console.log`/`dd`/`print` left over.
- Secrets not committed.

## Agent procedure per task

1. Read the task entry in full.
2. Open or confirm the `files` list.
3. Run `/tdd-red`:
   - Draft the test from the spec criterion named in `spec-ref`.
   - Ensure it fails for the right reason.
   - Commit.
4. Run `/tdd-green`:
   - Implement minimally.
   - Run full suite.
   - Commit.
5. Run `/tdd-refactor`:
   - Improve; rerun suite.
   - Commit or record skip-reason.
6. Mark the task `closed` in `tasks.md`. Append a one-liner with the three commit SHAs.

## Slash command

`/sdd-implement` orchestrates the loop for a task or a range. Per-beat commands are `/tdd-red`, `/tdd-green`, `/tdd-refactor`. See [`../commands/`](../commands/).

## Cross-references

- TDD basics: [`../philosophy/02-tdd.md`](../philosophy/02-tdd.md)
- Tasks: [`./04-tasks.md`](./04-tasks.md)
- Verify: [`./06-verify.md`](./06-verify.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`Test-after disguised as test-first`, `Mock creep`, `Refactor phase skipped systematically`)
