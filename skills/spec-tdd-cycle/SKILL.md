---
name: spec-tdd-cycle
description: Execute a single task's TDD cycle (Red → Green → Refactor) inside a spec-tdd-kit project. Use when the user references a task id (e.g., T042) or asks to "do the next task" / "run the TDD cycle" on an active feature.
---

# spec-tdd-cycle

You are executing one task's full Red-Green-Refactor cycle for an active feature in a spec-tdd-kit project.

## Preconditions

- `.specs/NNN-*/tasks.md` exists and contains the target task (or "next open").
- A Red commit for this task does not yet exist (or an earlier Red beat was partial).
- Tests currently green on the branch (baseline).

## Reading list

1. `~/Projects/spec-tdd-kit/AGENTS.md`
2. `~/Projects/spec-tdd-kit/phases/05-implement.md`
3. `~/Projects/spec-tdd-kit/philosophy/02-tdd.md`
4. The matching stack file in `~/Projects/spec-tdd-kit/stacks/`.

## Language protocol

- User: Spanish.
- Commit messages, tests, code: English.

## Steps

1. Identify the task.
   - If the user names one, use it.
   - Otherwise, pick the next `open` task in ID order.

2. Read the task entry in full: spec-ref, contract-ref, constitution-ref, DoD, R/G/F hints, files list.

3. Red beat — `/tdd-red`:
   - Author a test expressing one DoD bullet.
   - Run it; confirm the failure reason is semantic (feature absent/incorrect).
   - Commit `test: T{NNN} red — …` with the failure snippet in the body.

4. Green beat — `/tdd-green`:
   - Implement the minimum to pass.
   - Run full suite; all green.
   - Lint + types clean.
   - Commit `feat: T{NNN} green — …`.

5. Refactor beat — `/tdd-refactor`:
   - Apply behavior-preserving improvements.
   - Rerun suite after every change.
   - Commit `refactor: T{NNN} — …` or record "F: skipped — no smell detected" in `tasks.md`.

6. Update `tasks.md`:
   - `status: closed`.
   - `commits.red/green/refactor`: short SHAs.
   - `files`: adjust if reality differed.

7. Offer to run the next task.

## Stopping conditions

- Red cannot be made to fail for the right reason in 3 tries → ask the user; maybe the spec criterion is wrong.
- Green requires editing files outside the task's `files` list without a clear reason → ask.
- A previously green test breaks during Green → you overstepped; narrow or pause.
- Linter/type check fails during Green → fix before committing; no dirty Green.

## Tools you will use

- `Read`, `Write`, `Edit`, `Bash` for tests, code, and commits.
- Stack-specific subagents where configured (e.g., `laravel-feature-builder` for Laravel).
- `php-guidelines-from-spatie` skill for PHP work.
