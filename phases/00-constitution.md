# Phase 00 — Constitution

## Purpose

Define project-wide principles that **every** spec, plan, task, and commit must respect. The constitution is the only artifact in this kit that applies across all features.

## Artifact

`.specs/constitution.md` at project root. One document. Articled. Signed and dated implicitly by git.

## Structure

Populated from [`../templates/constitution.md`](../templates/constitution.md). Sections:

1. **Preamble** — one paragraph stating the project's purpose and the intended audience of the code.
2. **Articles** — numbered principles. Each article:
   - **Title** (5–10 words).
   - **Statement** (1–3 sentences in declarative form).
   - **Rationale** (1–2 sentences: why we commit to this).
   - **Enforcement** (how we verify: test, lint, review gate, ADR).
3. **Amendments** — log of article changes, each linked to an ADR.
4. **Brownfield sections** (only if the project was onboarded brownfield): `Current` (de facto rules extracted from the repo) and `Aspirational` (where we are going, with migration notes).

## Default article set

The kit mandates these articles (you may strengthen; you may not weaken):

1. **Spec-anchored development.** Every production change traces to a spec section. `verify` enforces.
2. **Test-first.** No production code is merged without a prior failing test. Enforcement: git history contains a `red` commit before a `green` commit for the same feature.
3. **Boundary-only mocks.** Mocks are permitted only at declared boundaries. Declared boundaries are listed in `Article: Boundaries`. Enforcement: review gate + stack-specific lint where available.
4. **Contract-first integration.** New integrations with an external system require a contract file in `contracts/` before consumer code. Enforcement: `tasks.md` must schedule contract tests before consumer tests.
5. **No silent clarification.** `[NEEDS CLARIFICATION: …]` markers are resolved only through explicit user dialog captured in `clarify.md` (or inlined spec update referencing it).
6. **ADR for non-local decisions.** Decisions with multi-task impact require an ADR.

## Optional article library

Pick what applies; skip the rest. If you pick, word it as enforceable.

- **Type safety.** `no any/mixed in exported signatures`. Enforced by tsc/psalm/phpstan/mypy.
- **Coverage floor.** `branch coverage ≥ 80% for domain/`. Enforced by test runner threshold.
- **Dependency hygiene.** `all runtime deps pinned; no beta versions without ADR`. Enforced by lockfile review.
- **Observability.** `every exception at a boundary is logged with correlation id`. Enforced by review gate.
- **Accessibility.** `WCAG 2.2 AA for every user-facing page`. Enforced by axe/pa11y in CI.
- **Security defaults.** `OWASP ASVS L1 minimum; L2 for money/PII surfaces`. Enforced by security-review skill before merge to main.
- **No YOLO migrations.** `data migrations are reversible or have a documented rollback`. Enforced by review gate + ADR.
- **Time is injected.** `domain code takes a Clock; no direct now()`. Enforced by lint rule or review.
- **Randomness is injected.** Analogous to time.
- **Feature flags expire.** `every flag has an owner and a review date`. Enforced by a scheduled check.

## How to declare boundaries

Add an article titled `Boundaries` listing entry points where mocking is allowed:

```
- HTTP out: <module/class>
- Database: <ORM/driver or raw client>
- Queues: <client>
- Clock: <injected Clock interface>
- Randomness: <injected Random interface>
- Filesystem: <FS abstraction>
- Third parties: <name each; list their adapters>
```

Any mock outside these boundaries is a review block.

## Amendment procedure

1. Draft an ADR in `.specs/adr/NNNN-amend-article-<id>.md` with: current wording, proposed wording, motivation, impact on existing specs/tasks, migration steps.
2. User approves.
3. Update `constitution.md`: the article is edited; the `Amendments` table at the bottom gets a new row linking to the ADR.
4. Commit: `spec: amend article N (see ADR NNNN)`.

## Brownfield specifics

On onboarding, the constitution has two parallel sections:

- **Current (de facto).** Observed in the code. Each article references an example file path.
- **Aspirational.** Where we are going. Each article has `applies_from: <commit|date>` and `migration: <policy>` fields.

When an aspirational article is fully realized (no violations remain), move it to `Current` via amendment.

## Agent behavior

- When Claude Code proposes a spec, plan, or task that violates any current article, it **stops** and surfaces the conflict.
- The user chooses: reshape or amend.
- The agent never silently downgrades.

## Typical size

A healthy constitution is **short**: 8–15 articles total. If you are past 20, the document has become a preference dump. Consolidate.

## References

- Template: [`../templates/constitution.md`](../templates/constitution.md)
- Checklist: [`../checklists/constitution.md`](../checklists/constitution.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`constitution creep`, `brownfield constitution invented, not extracted`)
