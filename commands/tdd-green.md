---
description: TDD Green beat — minimal code that makes the Red test pass, full suite green.
argument-hint: "<task id, e.g., T042>"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/05-implement.md
- @~/Projects/spec-tdd-kit/philosophy/02-tdd.md

Preconditions:
- A Red commit for this task exists in history.

Goal: implement the smallest code that turns the Red test green, without breaking other tests.

Steps:

1. Locate the task entry. Identify the failing test produced by the Red beat.

2. Implement minimally:
   - Only the code needed to pass the failing assertion.
   - No speculative additions ("while I'm here").
   - No premature abstraction.
   - Respect the `files` list; adding files requires noting it in `tasks.md`.

3. Run the **full** test suite (unit + contract + integration layers relevant to touched code). Required: all green.

4. If a previously green test breaks:
   - Narrow the change. You overstepped.
   - Or, if the change is legitimate and the old test is now wrong, stop and ask the user. Never silently modify another test to accommodate.

5. Run linter and type checker. Required: clean (or within declared exceptions).

6. Commit:
   ```
   feat: T{NNN} green — <one-line description>

   spec-ref: <section>
   ```

7. Return the commit SHA.

Reminders:
- Hardcode values if that is the smallest step; generalize only in the Refactor beat.
- No `console.log` / `dd` / `print` leftovers.
- Secrets stay out of the commit.
- If you notice a better design, write it down as a candidate for the Refactor beat; do not include it here.
