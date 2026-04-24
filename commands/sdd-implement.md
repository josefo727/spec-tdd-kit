---
description: Drive the TDD loop across tasks (SDD phase 5). Orchestrates /tdd-red, /tdd-green, /tdd-refactor.
argument-hint: "[optional: task id range like T012-T014, or 'all']"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/05-implement.md
- @~/Projects/spec-tdd-kit/philosophy/02-tdd.md

Preconditions:
- `.specs/NNN-*/tasks.md` exists with at least one open task.

Goal: execute tasks in order under the R-G-F cycle. One task at a time unless `[P]`.

Steps:

1. Identify the active feature folder and pick the next task:
   - If the user supplied a range, respect it.
   - Otherwise: next `open` task in ID order.
   - `[P]` tasks may run in parallel if the user asks; otherwise sequential.

2. Re-read the task entry in full. Confirm the `files` list and open them.

3. Run the cycle:
   a. Invoke `/tdd-red` for the task. Do not move on until the test fails for the right reason and the commit exists.
   b. Invoke `/tdd-green`. Do not move on until the full suite is green.
   c. Invoke `/tdd-refactor`. Accept "skipped — no smell detected" only if no refactor is warranted.

4. Update `tasks.md`:
   - Set `status: closed`.
   - Fill `commits.red / green / refactor` with short SHAs.
   - Update `files` if reality differed from the plan.

5. If a stopping condition arises (see phase doc: "Stopping conditions"), pause and surface to the user. Do not muscle through.

6. When the requested range or all open tasks are closed:
   - Offer to run `/sdd-verify` next.

Reminders:
- No Green without a preceding Red commit in the history.
- No Refactor that changes behavior.
- Lint and types must be clean after Green and after Refactor.
- Secrets must not be committed.
