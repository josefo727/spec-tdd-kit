# Stack — Generic (default)

Default guidance when no stack-specific appendix applies. Each stack file may override any of these defaults.

## Test runner

Any runner that supports:
- filtering by file/test name,
- watch mode,
- machine-readable output (JSON or TAP),
- deterministic ordering or seed flag.

## Folder layout

```
src/                  # production code
tests/
  unit/
  contract/
  integration/
  e2e/                # optional
  fixtures/
  exploratory/        # do not merge to main
```

## Naming

- Test file mirrors source path, suffix by runner convention.
- One top-level `describe` / `context` per source unit.
- One test per observable behavior. Multiple assertions per test only when they verify the same behavior.

## Fixtures

- Prefer factory functions over shared static fixtures.
- Factories expose only the inputs relevant to the test; others default.
- Keep fixtures small and expressive.

## Doubles

- Real collaborators by default (classicist).
- Mocks only at boundaries declared in the constitution.
- Prefer fakes (lightweight implementations) to mocks when possible.

## Time and randomness

- Inject a `Clock` and a `Random` through the class/function under test.
- Production wires to real `SystemClock` / `SystemRandom`.
- Tests wire to `FixedClock` / `SeededRandom`.

## Property-based testing

- Use a generator library (`fast-check`, `hypothesis`, `proptest`, `quickcheck`-like).
- At least one property per pure function whose domain is non-trivial.
- Seed the runner so failures are reproducible; pin seeds in the repo if needed.

## Contract tests

- Consumer side: assert we honor the contract when building requests / accepting responses.
- Provider verification: when owning the other side, test the real response matches the contract.
- When the provider is third-party, run a scheduled contract check (weekly minimum).

## Linting and types

- Linter errors fail CI.
- Type errors fail CI.
- Warnings are tolerated only when pre-existing and out of scope; new warnings are disallowed.

## CI

- Lint + types + unit + contract run on every PR.
- Integration runs on PR if the hermetic environment boots under ~60s. Otherwise, run on main + nightly.
- E2E runs on nightly or on-demand.

## Cross-reference

- TDD basics: [`../philosophy/02-tdd.md`](../philosophy/02-tdd.md)
- Implement: [`../phases/05-implement.md`](../phases/05-implement.md)
