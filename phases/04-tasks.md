# Phase 04 — Tasks

## Purpose

Decompose the plan into executable, test-first units. Each unit (a **task**) carries a Red-Green-Refactor sub-structure and enough metadata to be auditable.

## Artifact

`.specs/NNN-<kebab-name>/tasks.md`. Populated from [`../templates/tasks.md`](../templates/tasks.md).

## Structure of a task

```
T017 [P]
  title: Enforce configurable umbrella threshold
  spec-ref: §4.1 "umbrella threshold configurable"
  contract-ref: contracts/settings.yaml §threshold
  constitution-ref: Article II (test-first)
  DoD:
    - unit tests for threshold=0, 30, 100
    - config persisted across sessions
    - invalid values rejected with message
  R: failing test asserting needsUmbrella=true at probability=30 when threshold=30
  G: minimal implementation in logic/umbrella.{ts,php,py,…}
  F: extract predicate; deduplicate with precipitation normalization
  files:
    - src/logic/umbrella.ts
    - tests/logic/umbrella.test.ts
    - src/config/settings.ts
  status: open
```

Fields explained:

- **ID `T{NNN}`.** Unique within the feature. Zero-padded.
- **`[P]` flag.** Optional. Safe to run in parallel with other `[P]` tasks **if and only if** their `files` lists are disjoint.
- **Title.** Short, imperative.
- **`spec-ref`.** Pointer back to the spec section that motivates the task.
- **`contract-ref`.** Pointer to the contract file/section when applicable.
- **`constitution-ref`.** Article number(s) the task implements or honors.
- **DoD.** Checklist of acceptance observations for *this task*.
- **R / G / F.** The three beats. Each describes what will happen; the commits carry the output.
- **`files`.** Expected touched files. Used for `[P]` safety audit and for `verify` traceability.
- **`status`.** `open | in_progress | closed | skipped`. `skipped` requires a reason.

## Ordering rules

1. **Scaffolding before feature code.** Lint/format config, test runner init, CI baseline.
2. **Contract tests before consumers.** If task A tests a contract consumed by task B's unit, A goes first.
3. **Data migrations before code that requires the new columns.**
4. **Domain before UI.** Pure logic tested first; UI assembled around passing domain tests.
5. **Boundary adapters tested before feature logic that depends on them.**
6. **Cross-cutting (auth, observability) either at the start or at the end, never interleaved.**

## Parallelism

Mark `[P]` when:
- Files lists do not overlap.
- Tasks share no mutable in-memory state (e.g., two tasks editing the same migration file cannot be `[P]`).
- No implicit ordering exists via data or contracts.

Audit `[P]` flags before accepting `tasks.md`. A quick pass: list all files across `[P]` tasks; any duplicate line → offending tasks lose `[P]`.

## Granularity target

A task is a **single R-G-F cycle's worth of work**: minutes to small hours. If a task's `files` list exceeds 5 or its DoD exceeds 5 bullets, split it.

## Agent procedure

1. Read `spec.md`, `plan.md`, `contracts/`, `constitution.md`.
2. Draft candidate tasks covering:
   - every acceptance criterion (R1 of the fusion rules),
   - every boundary contract on the consumer and provider sides,
   - every constitution article with per-feature implications.
3. Assign IDs. Order per rules above. Mark `[P]` conservatively.
4. Fill `files` optimistically; update during implement if reality differs.
5. Present `tasks.md` to the user in Spanish. Flag any assumption.
6. Commit: `plan: NNN tasks breakdown`.

## Re-planning mid-feature

If `implement` reveals the task list is wrong (missing task, wrong order, incorrect contract), update `tasks.md` with a visible edit:

- Append a `## Amendments` section with dated entries.
- Do not silently reorder closed tasks.
- If a new task is inserted mid-sequence, give it a next-free number, not a decimal suffix.

## Slash command

`/sdd-tasks` writes this artifact. See [`../commands/sdd-tasks.md`](../commands/sdd-tasks.md).

## Cross-references

- Template: [`../templates/tasks.md`](../templates/tasks.md)
- Implement: [`./05-implement.md`](./05-implement.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`Parallel-[P] abuse`)
