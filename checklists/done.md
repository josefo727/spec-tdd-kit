# Checklist — Done (per feature)

Run during `/sdd-verify`. All items must PASS (or WARN with rationale) before closing a feature.

## Tests

- [ ] Full test suite green locally and in CI.
- [ ] Every acceptance criterion in `spec.md` has at least one green test referencing it (R1).
- [ ] No orphan tests (each test ties to a criterion or contract) except `tests/exploratory/`.
- [ ] Red/Green/Refactor commits exist in history for every closed task (or documented rationale for missing beats).

## Contracts

- [ ] Consumer-side contract tests green.
- [ ] Provider verification (when owned) green.
- [ ] Scheduled contract check in place for third-party-owned providers.
- [ ] Contract `version` and `captured` consistent with current code.

## Constitution

- [ ] Mandatory articles honored in the new code.
- [ ] Optional articles in force honored.
- [ ] Any deviation has an ADR and a scheduled migration.

## Static quality

- [ ] Linter clean (no new warnings).
- [ ] Type checker clean.
- [ ] No `console.log` / `dd` / `print` / `debugger` leftovers.
- [ ] No secrets committed.

## Research freshness

- [ ] No `research.md` entry older than 60 days without re-query.

## Observability

- [ ] New boundaries log with correlation id where constitution mandates.
- [ ] Metrics/traces wired if applicable.

## Security (conditional)

- [ ] If feature touches auth / money / PII / external writes: security-review skill run, findings resolved or filed.

## Docs

- [ ] User-visible strings and help text reflect new behavior.
- [ ] README / ops docs updated.

## Project changelog

- [ ] Project's `CHANGELOG.md` or release notes updated.

## Artifacts

- [ ] `spec.md` has `## Closed` footer.
- [ ] `tasks.md` shows verify SHA.
- [ ] `.specs/index.md` reflects the module under SDD+TDD (brownfield).

## Final commit

- [ ] `spec: NNN closed — verify green`.
