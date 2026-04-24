# 03 — SDD + TDD fusion

## The thesis

SDD and TDD answer different questions:

- **SDD**: *What should the software do, and why?*
- **TDD**: *How do we build it safely, piece by piece?*

Each is partial. SDD without TDD produces well-documented drift — a spec that describes a system nobody verified. TDD without SDD produces well-tested improvisation — a suite that covers the code you happened to write, not the code the stakeholders needed. The kit composes both.

## Where they touch

The hand-off is the **tasks phase**:

```
constitution ─┐
              │
         specify ── clarify ── plan ── tasks ──→ implement ── verify
                                   │                │
                                   │ acceptance     │ Red → Green → Refactor
                                   │ criteria       │ per task
                                   ▼                ▼
                               test targets     test execution
```

- `spec.md` lists **acceptance criteria** in observable terms.
- `plan.md` and `contracts/` refine those criteria into **test targets** (unit, contract, integration, e2e).
- `tasks.md` emits **one task per test target** (or per cohesive cluster), each with R-G-F sub-steps.
- `implement` executes the cycle.
- `verify` checks that every acceptance criterion is covered by at least one green test.

## Rules of engagement

### R1 — Every acceptance criterion maps to at least one test

A criterion without a test is an assertion nobody is defending. The `/sdd-verify` command flags orphans.

### R2 — Every task has a spec-ref

Tasks are not free-standing. An entry like `T042` must reference the spec section that motivates it: `spec-ref: §3.2 "user can request refund"`. The reference makes the chain auditable.

### R3 — Red commit exists for every feature

A search of the feature's commit history must show at least one `red` commit before the first `green` commit of that feature. Hooks or CI can enforce this (optional).

### R4 — Constitution articles become either tests or review gates

For every article in `constitution.md`:

- If the article is **machine-checkable** ("coverage ≥ 80%", "no `any` types in exported interfaces"), add a lint/test that enforces it.
- If the article is **reviewer-checkable** ("no mocks in domain tests"), add it to [`checklists/done.md`](../checklists/done.md) and to the PR template.

An article that is neither is not an article — it is a preference. Move it to stack appendix.

### R5 — Contracts feed contract tests before feature tests

In `tasks.md`, contract tests are scheduled before the feature tests that depend on the boundary. If the contract is wrong, feature tests should not compile.

### R6 — Amendments to the spec update the tests

If a `verify` check reveals an acceptance criterion changed without a test change, the feature is **not done**. The developer either adds the test or reverts the criterion.

## The task as the atomic unit

A task in `tasks.md` is the smallest SDD-TDD quantum:

```
T017 [P] spec-ref: §4.1 "umbrella threshold configurable"
     contract-ref: contracts/settings.yaml
     constitution-ref: Article II (test-first)
     DoD: test for threshold=0, threshold=100, threshold=30; config persists; UI reflects.
     R: write failing test for threshold=30 returning needsUmbrella=true at probability=30
     G: minimal implementation in logic/umbrella.ts
     F: extract predicate, rename, deduplicate with precipitation normalization
```

Each of R, G, F maps to a commit (or a squashed equivalent). The task is closed only when F is done or explicitly skipped with a reason.

## Two failure modes the fusion prevents

**Spec without tests ("documented drift").** SDD alone can produce this. The fusion requires R1; the `/sdd-verify` command fails when a criterion has zero test coverage.

**Tests without spec ("covered but wrong").** TDD alone can produce this. The fusion requires R2; every task traces to a spec section; `tasks.md` can be regenerated if the spec changes.

## The feedback loop

```
                 clarify
                    ▲
                    │
                    │ ambiguity found
                    │
spec ── plan ── tasks ── implement ── verify
 ▲                              │
 │                              │ criterion unmet OR
 │                              │ new scope discovered
 └──────────────────────────────┘
                amend spec
```

The loop runs per feature and per amendment. Short is better: the faster the loop, the sooner a wrong assumption surfaces. Target: first green commit within one session for small features.

## When the fusion hurts

- **Throwaway prototypes**. The overhead is not worth it. Write the code, throw it away, write the spec once you know what you learned.
- **Spikes**. Same rationale. Tag the branch as a spike; do not merge to main.
- **Hotfixes**. Write the Red test, then Green, skip the spec update, and schedule a follow-up ADR.

## When the fusion pays off fastest

- Multi-contributor projects: the spec coordinates.
- Projects touching money, security, or PII: the audit trail matters.
- Long-lived codebases: drift compounds; the fusion caps it.

## Cross-references

- SDD: [`01-sdd.md`](./01-sdd.md)
- TDD: [`02-tdd.md`](./02-tdd.md)
- Implement phase: [`../phases/05-implement.md`](../phases/05-implement.md)
- Verify phase: [`../phases/06-verify.md`](../phases/06-verify.md)
- Tasks template: [`../templates/tasks.md`](../templates/tasks.md)
