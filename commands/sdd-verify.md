---
description: Run the SDD+TDD verify checks (R1–R10) for the active feature.
argument-hint: "[--partial]"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/06-verify.md
- @~/Projects/spec-tdd-kit/checklists/done.md

Goal: produce a verify report for the active feature and gate closure.

If `--partial`, run R1–R5 only and report; no closing commit.

Steps:

1. Identify the active feature folder.

2. Run:
   - The test suite (full). Must be green. Capture duration and failing tests.
   - Linter. Must be clean or within declared exceptions.
   - Type checker. Must be clean.

3. R1 — Spec coverage:
   - For each acceptance criterion in `spec.md`, find at least one test whose commit (Red) mentions the `spec-ref`.
   - Report orphans.

4. R2 — Task completeness:
   - Every task in `tasks.md` is `closed` or `skipped` with reason.
   - Each closed task has three commit SHAs or documented rationale.

5. R3 — Orphan tests:
   - Tests with no spec-ref or contract-ref (ignoring `tests/exploratory/`).

6. R4 — Contracts match reality:
   - Contract tests green.
   - Version / captured dates consistent.

7. R5 — Constitution compliance:
   - Machine-checkable articles: run the checks.
   - Review-gate articles: walk `../checklists/done.md` with the user.

8. R6 — Research freshness:
   - Any `research.md` entry older than 60 days: flag. Offer to re-query context7 now.

9. R7 — Observability:
   - If applicable, confirm correlation IDs propagate through new code; exceptions at boundaries are logged.

10. R8 — Security review (conditional):
    - If the feature touches auth / money / PII / external writes, run the `security-review` skill.

11. R9 — Docs:
    - READMEs / help text updated when user-facing behavior changed.

12. R10 — Changelog:
    - Append entry in the project's CHANGELOG.md (not the kit's).

13. Produce a structured report with PASS / WARN / FAIL per check.

14. Decisions:
    - All PASS (WARN acceptable): close the feature:
      - Append `## Closed` footer in `spec.md`.
      - Update `tasks.md` with verify SHA.
      - Update `.specs/index.md` if brownfield.
      - Commit: `spec: NNN closed — verify green`.
    - Any FAIL: surface to the user and propose the minimal remediation (back to the relevant phase).

Reminders:
- Skipping verify on small features is a listed anti-pattern.
- Never auto-close while a FAIL exists.
