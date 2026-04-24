# Test plan — NNN-<kebab-name>

> Optional. Use when testing strategy is non-trivial: many layers, flaky third parties, high latency boundaries, asynchronous flows, or distributed systems. Otherwise, the `plan.md` §Test strategy is enough.

## Overview

`<One paragraph: what we test, where, and why this feature justifies a dedicated test plan.>`

## Layers

### Unit

- Scope: `<modules>`
- Runner: `<framework>`
- Fixtures: `<how produced>`
- Coverage target: `<if any>`

### Contract

- Consumer-side: `<list of contracts tested; runner>`
- Provider verification: `<if applicable>`

### Integration

- Hermetic environment: `<docker-compose | testcontainers | local fake | in-memory>`
- What the env includes: `<services>`
- Teardown strategy: `<per-test, per-file, per-suite>`
- Data seeding: `<how>`

### End-to-end (optional)

- Tooling: `<Playwright | Cypress | Detox | custom>`
- Scenarios (bind to acceptance criteria): `<list>`
- Non-determinism handling: `<…>`

## Cross-cutting concerns

- **Time.** `<Clock injection, fake clock strategy>`
- **Randomness.** `<Seeded generators, deterministic fixtures>`
- **Parallelism.** `<Runner-level parallel safe? Shared fixtures?>`
- **Network.** `<Allowed / mocked / recorded>`
- **Database.** `<Transaction per test | truncation | migrations reset>`

## Anti-tautology measures

- Tests authored from acceptance criteria, not from diff of implementation.
- At least `<N>` property-based or boundary tests per pure function.
- Review step: can the implementation be rewritten differently and still pass the same tests?

## Performance / load (optional)

- Budgets: `<p50, p95, p99>`
- Tooling: `<k6 | artillery | wrk>`
- Scenarios: `<list>`

## Security tests (optional)

- Threat model linkage: `<ADR or doc>`
- Checks: `<SAST | DAST | fuzz | manual review>`

## Flaky-test policy

- Flaky test detection: `<runner report | retry counter | quarantine folder>`
- Quarantine max age: `<N days>`
- Remediation rule: `<fix or delete>`

## Matrix (if applicable)

| Env | Runtime | DB | Cache |
|-----|---------|----|-------|
|     |         |    |       |

## CI wiring

- Pipelines: `<which jobs run which layers>`
- Parallelism: `<shards>`
- Cache strategy: `<…>`
