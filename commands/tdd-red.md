---
description: TDD Red beat — write a failing test that fails for the right reason.
argument-hint: "<task id, e.g., T042>"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/05-implement.md
- @~/Projects/spec-tdd-kit/philosophy/02-tdd.md

Goal: produce a committed failing test for the named task.

Steps:

1. Locate the task entry in `tasks.md`. If the argument is missing, ask the user which task.

2. Read:
   - The task's `spec-ref` section in `spec.md`.
   - The task's `contract-ref` if any.
   - The stack file in `~/Projects/spec-tdd-kit/stacks/` that matches the project.

3. Identify the **one** DoD bullet you will target in this Red beat. Multiple Red commits per task are fine; one at a time.

4. Write the test:
   - Assert on observable behavior, not implementation internals.
   - Prefer classicist style for domain; mock only at declared boundaries.
   - File location per stack conventions.

5. Run the test. Required: it fails for the **right reason** — the feature is absent or behaves incorrectly. If it fails because of unknown imports, missing stubs, typos in expected values:
   - Fix scaffolding (stubs that throw NotImplemented, imports), or
   - Correct the expected value.
   - Re-run until the failure is semantic.

6. Capture the failure output (the minimal lines that describe the failure).

7. Commit with message:
   ```
   test: T{NNN} red — <one-line description>

   Observed failure:
     <captured output>

   spec-ref: <section>
   ```

8. Return the commit SHA for `tasks.md` update.

Reminders:
- Do not write production code yet.
- If the test requires a collaborator, create a thin stub that throws NotImplemented; the Green beat implements it.
- The Red commit is the evidence that this task's test was authored before its implementation. It must exist in history.
