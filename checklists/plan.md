# Checklist — Plan

Use before committing `plan.md`, `research.md`, and `contracts/`.

## Plan content

- [ ] Summary paragraph mapping spec to proposed architecture.
- [ ] Stack decision table filled; every row has a rationale linking to research or a constitution article.
- [ ] Module layout present and consistent with the stack appendix.
- [ ] Data model: entities, relationships, invariants. Migrations named.
- [ ] Boundaries table: every external interaction has a contract file.
- [ ] Error model listing exception types/codes and user-facing surfaces.
- [ ] Observability section: logs, metrics, traces, correlation ids.
- [ ] Security section: authn, authz, input validation, secrets.
- [ ] Test strategy names layers, runners, and hermetic environments.
- [ ] Rollout plan (flag, migration order, rollback) — required if multi-deploy impact.

## Research

- [ ] One entry per external library or service touched.
- [ ] Every entry has `captured: YYYY-MM-DD` not older than 60 days.
- [ ] Every entry has `source:` (context7 preferred; WebFetch acceptable).
- [ ] Every entry ties to a specific plan section.

## Contracts

- [ ] At least one contract per external boundary.
- [ ] Every contract file has `version` and `captured` fields.
- [ ] HTTP contracts in `openapi.yaml` (or minimal `http.yaml`).
- [ ] Queue/event contracts in JSON Schema.
- [ ] Internal boundaries as typed interface files in the stack's language.

## ADRs

- [ ] Decisions with multi-task impact have an ADR.
- [ ] New dependencies / runtimes / services have an ADR.
- [ ] Constitution amendments, if any, have an ADR linked from `Amendments` table.

## Constitution compliance

- [ ] No plan decision violates an article.
- [ ] New boundary, if any, is declared in Article VII via an amendment (ADR).

## Approval

- [ ] User reviewed plan in Spanish.
- [ ] Tradeoffs explicitly surfaced and acknowledged.
- [ ] Commit: `plan: NNN initial plan + research + contracts`.
