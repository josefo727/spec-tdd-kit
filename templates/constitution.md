# Project Constitution

> Copy this template to `.specs/constitution.md` at project root. Replace every `<PLACEHOLDER>`.

## Preamble

`<One paragraph: the purpose of this software, its primary users, and the bar we hold ourselves to.>`

## Articles

### Article I — Spec-anchored development

**Statement.** Every production change traces to a spec section. Spec and code are committed together when behavior changes.

**Rationale.** Prevents documented drift.

**Enforcement.** `verify` phase check R1; review gate.

---

### Article II — Test-first

**Statement.** No production code is merged without a prior failing test. The git history shows a `red` commit before the corresponding `green` commit within the same feature.

**Rationale.** Design feedback and regression safety.

**Enforcement.** Commit history audit during `verify`; optional `pre-push` hook.

---

### Article III — Boundary-only mocks

**Statement.** Doubles are permitted only at declared boundaries (see Article VII). Domain tests use real collaborators.

**Rationale.** Avoid tautology tests and mock creep.

**Enforcement.** Review gate; stack-specific lint where available.

---

### Article IV — Contract-first integration

**Statement.** New integrations with an external system require a contract file in `contracts/` before consumer code.

**Rationale.** Stable boundaries; contract tests discover drift early.

**Enforcement.** `tasks.md` scheduling rule; `verify` R4.

---

### Article V — No silent clarification

**Statement.** `[NEEDS CLARIFICATION: …]` markers are resolved only through explicit user dialog captured verbatim in the clarify log (or inlined spec update citing it).

**Rationale.** Preserves stakeholder intent; avoids LLM confabulation.

**Enforcement.** `clarify` phase tool preference; `verify` orphan-marker check.

---

### Article VI — ADR for non-local decisions

**Statement.** Decisions that affect more than one task, introduce a new dependency/runtime/service, or amend an article require an ADR in `.specs/adr/`.

**Rationale.** Auditability of architecture evolution.

**Enforcement.** Review gate; ADR template linkage.

---

### Article VII — Boundaries

**Statement.** The following are the declared boundaries of this project. Mocks are permitted here and only here:

- `<HTTP out: <module/class>>`
- `<Database: <ORM/driver>>`
- `<Queue: <client>>`
- `<Clock: <injected Clock>>`
- `<Randomness: <injected Random>>`
- `<Filesystem: <FS abstraction>>`
- `<Third parties: <each name and its adapter>>`

**Rationale.** Uniform isolation policy; enables classicist testing of domain.

**Enforcement.** Review gate; plan must map every external interaction to a boundary here.

---

### Article VIII — `<YOUR ARTICLE>`

`<Delete or fill in. Word the statement as enforceable.>`

---

## Brownfield sections (delete for greenfield projects)

### Current (de facto)

- `<Rule observed in the repo>` — evidence: `<file path>`
- …

### Aspirational

- `<Rule we commit to going forward>` — applies_from: `<commit|date>`; migration: `<policy>`; ADR: `<link>`
- …

## Amendments

| Date | Article | Change | ADR |
|------|---------|--------|-----|
|      |         |        |     |
