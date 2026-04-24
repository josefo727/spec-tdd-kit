# 01 — Spec-Driven Development

## Premise

Software drifts from its author's intent faster than most teams can document. LLM-assisted development amplifies this: a prompt-of-the-moment produces runnable code that nobody specified and nobody can easily amend. **SDD inverts the relationship**: the specification is the authored artifact, and the code is a derivation checked against the spec.

## Levels (Böckeler's taxonomy)

| Level | Spec role | Code role | Typical signal |
|---|---|---|---|
| **Spec-first** | Authored once to launch a feature. | Authored from then on. | "We wrote a PRD, now we code." |
| **Spec-anchored** | Maintained alongside code. Changes to one require changes to the other. | Implementation of the spec. | "If the spec and code disagree, we pick and fix." |
| **Spec-as-source** | The source. Edited by humans. | Generated or tightly derived from the spec. | "We edit the spec; the code re-materializes." |

This kit targets **spec-anchored**. Spec-as-source is an aspiration; it fails today for non-trivial stacks because generation quality is uneven and cross-file edits are fragile.

## Why spec-anchored, not spec-first

Spec-first is indistinguishable from **spec-once** in practice. The spec is born; the code grows; they part ways. Spec-anchored demands:

- The spec is versioned with the code in the same repo.
- Every feature commit touches both, or the commit is a deliberate spec-only or code-only change (documented as such).
- The verify phase asserts that the spec still describes the code.

## What a spec is, and is not

A **spec** states, for a bounded slice:

- The user story (who, what, why).
- The acceptance criteria (observable behavior).
- Non-goals (what the slice does **not** cover).
- Constraints from the constitution that apply here.
- Ambiguities marked `[NEEDS CLARIFICATION: …]`.

A spec is **not**:

- A technical plan. No stack, no architecture. That lives in `plan.md`.
- A user manual. No marketing copy.
- A wish list. Everything listed is in scope for the feature.
- A PRD in the traditional sense. It is read by a machine; phrasing matters.

## The constitution layer

Above individual specs sits the **constitution**: immutable project-wide principles. Every spec and every plan must be consistent with the constitution. When a spec cannot comply, you either:

- reshape the spec, or
- amend the constitution via an ADR.

You do not proceed while in violation.

## Artifacts in this kit

Per feature:
- `spec.md` — WHAT/WHY.
- `plan.md` — HOW at design level.
- `research.md` — evidence backing the plan.
- `contracts/` — interface contracts.
- `tasks.md` — executable breakdown.

Per project:
- `constitution.md` — principles.
- `adr/NNNN-*.md` — decisions that shaped the plan or amended the constitution.

## Compared with BDD, DDD, TDD

- **BDD (Behavior-Driven Development)** — executable specs in Gherkin. The kit borrows BDD's "acceptance criteria first" stance but does not require Gherkin syntax. Your acceptance criteria can become BDD scenarios in the stack appendix if you like.
- **DDD (Domain-Driven Design)** — a way to organize the domain. Orthogonal to SDD. DDD concepts (bounded contexts, ubiquitous language) are natural constitution articles.
- **TDD (Test-Driven Development)** — a tactic. SDD gives the test targets (acceptance criteria, contracts); TDD gives the discipline to reach them. See [`03-sdd-tdd-fusion.md`](./03-sdd-tdd-fusion.md).

## SDD done poorly

- A 40-page spec nobody reads.
- A spec that names React components. That is a plan.
- A spec updated only at release time.
- A spec that encodes the developer's planned code in prose.

## SDD done well

- A short spec per feature, cross-referenced from the constitution.
- A living `tasks.md` whose items trace back to acceptance criteria.
- A plan whose decisions each trace to research or to a constitution article.
- A `verify` phase that catches drift before release.

## References inside the kit

- Constitution: [`../phases/00-constitution.md`](../phases/00-constitution.md)
- Spec phase: [`../phases/01-specify.md`](../phases/01-specify.md)
- Plan phase: [`../phases/03-plan.md`](../phases/03-plan.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`spec-once`, `vibe coding`, `constitution creep`)
