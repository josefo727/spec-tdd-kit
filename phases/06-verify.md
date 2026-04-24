# Phase 06 — Verify

## Purpose

Before a feature is considered done, prove that **spec, plan, tasks, tests, and code agree**. Catch drift before it escapes.

## Artifact

No new persistent file by default. The verify run produces a report in the conversation and updates `tasks.md`/`spec.md` closing footers.

## Checks

### R1 — Spec coverage

Every acceptance criterion in `spec.md` maps to at least one **green** test.

- Walk the criteria.
- For each, grep for its `spec-ref` in the test tree (the Red test commits left a marker).
- Zero orphans.

### R2 — Task completeness

Every task in `tasks.md`:
- Has `status: closed` or `status: skipped` (with reason).
- Carries three commit SHAs (or a reason for fewer).
- `files` actually touched match the list within a reasonable delta.

### R3 — Orphan tests

Every test file ties to at least one acceptance criterion or to an existing contract. Discovery tests or exploratory tests allowed in dedicated folder (`tests/exploratory/`), not merged to main.

### R4 — Contracts match reality

For each contract in `contracts/`, confirm:
- Consumer side honored (contract tests green).
- Provider side honored, where accessible (provider verification or schema introspection).
- Version field matches the current code.

### R5 — Constitution compliance

For each article with machine checks, run the check (lint, type, coverage, security scan). For review-gate articles, run the [`checklists/done.md`](../checklists/done.md).

### R6 — Research freshness

Each `research.md` entry older than 60 days is re-validated against `context7`. Update the `captured` date if findings still hold; otherwise amend the plan.

### R7 — Observability and logs

If the constitution declares observability, confirm correlation IDs flow through new code paths and new exceptions are logged at boundaries.

### R8 — Security review (conditional)

If the feature touches auth, money, PII, or external writes, run a security review pass (e.g., invoke the `security-review` skill; see [`../tools/claude-code-skills.md`](../tools/claude-code-skills.md)).

### R9 — Docs / READMEs / help text

User-visible strings and ops docs updated when behavior changed.

### R10 — Changelog (project, not kit)

Append the feature to the project's own `CHANGELOG.md` or release notes where convention exists.

## Agent procedure

1. Run the test suite (full). Must be green.
2. Run linter and type checker. Must be clean or within allowed exceptions.
3. Walk R1–R10 checks in order, producing a structured report:

```
# Verify report — 001-umbrella-app
- R1 Spec coverage: PASS (5/5 criteria covered)
- R2 Task completeness: PASS (17/17 tasks closed)
- R3 Orphan tests: PASS (0 orphans)
- R4 Contracts: PASS (2/2 contracts verified)
- R5 Constitution: PASS (Articles 1–6 green)
- R6 Research freshness: PASS (3 entries, all <60 days)
- R7 Observability: PASS
- R8 Security: N/A (no money/PII/auth changes)
- R9 Docs: WARN — README missing "Usage" update
- R10 Changelog: PASS
```

4. Surface failures and warnings to the user.
5. For each failure, propose the smallest remediation:
   - Missing test → loop back to `implement`.
   - Spec drift → loop back to `specify`/`clarify`.
   - Contract mismatch → loop back to `plan`.
6. When all PASS (warnings acceptable), close:
   - Append `## Closed` footer to `spec.md`.
   - Update `tasks.md` with the verify SHA.
   - If brownfield, update `.specs/index.md`.
   - Commit: `spec: NNN closed — verify green`.

## Running verify early

You can run `/sdd-verify --partial` at any point during `implement` to catch gaps while cheap. The full verify is gated on all tasks closed.

## Slash command

`/sdd-verify`. See [`../commands/sdd-verify.md`](../commands/sdd-verify.md).

## Cross-references

- Checklists: [`../checklists/done.md`](../checklists/done.md)
- Fusion rules: [`../philosophy/03-sdd-tdd-fusion.md`](../philosophy/03-sdd-tdd-fusion.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`Skipping the verify phase on small features`)
