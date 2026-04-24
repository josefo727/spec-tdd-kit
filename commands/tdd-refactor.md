---
description: TDD Refactor beat — clean while green. Behavior-preserving only.
argument-hint: "<task id, e.g., T042>"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/05-implement.md

Preconditions:
- Green commit for this task exists. All tests green.

Goal: improve structure without changing behavior. If nothing needs refactoring, record that explicitly.

Steps:

1. Identify candidates:
   - Names that do not match intent.
   - Duplication across the new code and adjacent code.
   - Function/class doing more than one thing.
   - Hidden coupling (implicit globals, long parameter lists).
   - Readability wins (extract, inline, rename, guard clauses, early returns).
   - Missing edge-case tests surfaced during Green — add the test (becomes a mini Red beat), then refactor.

2. Refactor in small steps:
   - One kind of change at a time (rename, then extract, then dedupe).
   - Run the full test suite after every step.
   - Run linter and type checker.

3. If a step reveals a bug (test newly fails), stop, create a Red test for it, fix, continue.

4. When done, commit:
   ```
   refactor: T{NNN} — <what improved>
   ```

5. If nothing warrants refactor:
   - Update `tasks.md` task entry: `F: skipped — no smell detected`.
   - No commit needed for the skip itself; note in the task.
   - Warning: repeated skips across the feature are a smell; the Verify phase audits the pattern.

Reminders:
- Never change behavior during Refactor. If you need to, that is a new R-G-F cycle.
- Keep the refactor scope local; wide refactors belong in their own task.
- Record reasoning for non-obvious refactors in the commit body.
