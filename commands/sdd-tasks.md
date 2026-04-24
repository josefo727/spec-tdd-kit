---
description: Break the plan into ordered, test-first tasks with R-G-F beats (SDD phase 4).
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/04-tasks.md
- @~/Projects/spec-tdd-kit/templates/tasks.md

Preconditions:
- `.specs/NNN-*/spec.md`, `plan.md`, and `contracts/` exist and are approved.

Goal: produce `.specs/NNN-*/tasks.md` with ordered tasks, each carrying R-G-F sub-steps and metadata.

Steps:

1. Identify the active feature folder.

2. Enumerate task candidates by walking:
   - Every acceptance criterion in `spec.md` (one or more tasks each).
   - Every contract in `contracts/` (a consumer contract test, plus a provider test if we own the provider).
   - Every constitution article with per-feature implications.
   - Scaffolding needs (test runner bootstrap, lint config, CI tweaks) if not already in place.

3. Order the tasks:
   - Scaffolding first.
   - Contract tests before consumers.
   - Data migrations before code requiring the new columns.
   - Domain before UI.
   - Boundary adapters tested before logic that depends on them.
   - Cross-cutting either first or last.

4. Assign IDs `T001`, `T002`, … zero-padded.

5. For each task, fill:
   - title
   - spec-ref (with section header)
   - contract-ref (if applicable)
   - constitution-ref
   - DoD (3–5 bullets max)
   - R: one sentence describing the failing test
   - G: one sentence describing the minimal implementation
   - F: one sentence naming likely refactors; "skipped — no smell detected" allowed later
   - files (optimistic; updated during implement)
   - status: open
   - commits: empty

6. Mark `[P]` on tasks whose `files` lists are disjoint AND share no mutable state. Audit after the first pass: if any two `[P]` tasks share a file path, remove `[P]` on both.

7. Size: target minutes-to-small-hours per task. If a task's DoD exceeds 5 bullets or `files` exceeds 5 entries, split it.

8. Present `tasks.md` to the user in Spanish, showing:
   - Total number of tasks.
   - Parallelism summary.
   - Any assumption the user must confirm.

9. On approval, commit: `plan: NNN tasks breakdown`.

Reminders:
- No free-standing tasks: every task has a `spec-ref`.
- Avoid inventing work not present in the plan; surface gaps instead.
- Do not reorder tasks after implement starts without an Amendments entry.
