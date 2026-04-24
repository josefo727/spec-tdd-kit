# 02 — Test-Driven Development

## The cycle

```
    ┌──────────┐   ┌──────────┐   ┌──────────┐
    │   RED    │ → │  GREEN   │ → │ REFACTOR │ → (back to RED)
    └──────────┘   └──────────┘   └──────────┘
```

1. **Red.** Write a test that fails for the right reason. The feature does not exist, or it behaves incorrectly. Observe the failure; the output is part of the evidence that this step was real.
2. **Green.** Write the smallest amount of production code that makes the test pass. No speculation. No "while I'm here".
3. **Refactor.** With all tests green, improve structure — rename, extract, deduplicate, tighten types — without changing behavior. Run the full test suite after every edit.

## Why the cycle

- **Red guarantees the test exercises the change.** A test written after the code often passes trivially; you cannot tell whether it would catch a regression.
- **Green limits scope.** The code you write is the code demanded by the failing assertion. You stop building beyond it.
- **Refactor pays down design debt continuously.** The cycle's cadence keeps debt small enough to clear in minutes.

The three beats are **separate commits** whenever practical. That makes the history auditable: reviewers see a failing test, then its minimal pass, then the polish.

## What counts as "failing for the right reason"

A test that fails because it cannot compile is not Red. Fix the scaffolding first (imports, file paths, stub function existing but throwing `NotImplementedError`), then see the assertion fail.

A test that fails because of a typo in the expected value is not Red. Rewrite until the failure describes the missing behavior.

## Classicist vs. mockist — the kit's default

Two schools of TDD exist:

- **Classicist (Chicago).** Test behavior. Real collaborators. Isolation via process boundaries, not doubles.
- **Mockist (London).** Test each unit in isolation. Doubles everywhere.

**Default in this kit:**

- **Domain logic and pure functions** → classicist. Real collaborators. No mocks.
- **Boundaries** (HTTP, filesystem, clock, randomness, queues, databases over the wire, third parties) → mockist by default, backed by one integration test per boundary per feature that exercises the real thing in a hermetic environment.

The constitution declares which boundaries are "allowed mock targets". Anything not listed is domain and must be tested with reality.

## Test shapes

- **Example tests.** "Given input X, produce output Y." The backbone.
- **Boundary tests.** "Empty list", "null", "max int", "very large string", "invalid UTF-8", "duplicate keys". Cover systematically; do not rely on the example set.
- **Property tests.** "For all inputs drawn from this generator, invariant P holds." Useful for pure functions and for data transforms. Stack appendices name the library (Hypothesis, fast-check, proptest, QuickCheck).
- **Contract tests.** Assert your code honors the contract with an external system. Paired with **provider verification** on the other side when possible.
- **Integration tests.** The wiring. One per boundary, at minimum, per feature.
- **End-to-end tests.** Optional. Use sparingly; they are slow and brittle. If you have them, tie them to acceptance criteria, not to internals.

## The tautology trap

The most common failure mode of test-after (and of lazy test-first) is a test that mirrors the implementation so precisely that it only verifies "I wrote what I wrote". Defenses:

1. Write tests from the **spec's acceptance criteria**, not from the code.
2. Prefer assertions on **observable outcomes** (return value, state change, side effect), not on implementation steps ("then method foo was called").
3. Include at least one **boundary** and one **property** test per non-trivial unit.
4. If you can delete the implementation and reimplement differently without changing the tests, the tests probably avoid the trap.

## TDD done poorly

- Writing all tests up front, then all code. That is "test-first" without cycle; you lose the design feedback.
- Green commits with no Red precedent in the history.
- Mocks in 100% of tests; nothing tested against reality.
- Skipping Refactor forever.
- Tests that test the framework ("Laravel returns 200 on `GET /`").

## TDD done well

- Three commits per task (`red`, `green`, `refactor`), merged or squashed only after review.
- Tests in the language of the spec, not of the code.
- A test suite that runs locally in under a minute for domain, under ten for full.
- A refactor beat that routinely improves names, boundaries, and coverage of edge cases.

## Integration with `implement` phase

The kit's `implement` phase is TDD line by line. See [`../phases/05-implement.md`](../phases/05-implement.md). Each task in `tasks.md` carries R-G-F sub-steps. Slash commands `/tdd-red`, `/tdd-green`, `/tdd-refactor` walk you through each beat with guardrails.

## References

- Fusion: [`03-sdd-tdd-fusion.md`](./03-sdd-tdd-fusion.md)
- Implement phase: [`../phases/05-implement.md`](../phases/05-implement.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`tautology tests`, `test-after disguised as test-first`, `mock creep`, `refactor phase skipped systematically`)
- Stack-specific testing: [`../stacks/`](../stacks/)
