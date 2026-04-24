# Plan — NNN-<kebab-name>

> Copy to `.specs/NNN-<kebab-name>/plan.md`.

## Summary

`<Paragraph mapping the spec to the proposed architecture.>`

## Stack decision

| Item | Choice | Rationale |
|------|--------|-----------|
| Language | `<e.g., TypeScript 5.x>` | `<research ref or constitution article>` |
| Framework | `<e.g., Vue 3 / Laravel 11 / Axum>` | `<…>` |
| Test runner | `<e.g., Vitest / Pest / pytest>` | `<…>` |
| Linter | `<…>` | `<…>` |
| Type checker | `<…>` | `<…>` |
| Build tool | `<…>` | `<…>` |
| Runtime target | `<…>` | `<…>` |

## Module layout

```
<directory tree outline>
```

Entry points:
- `<path>` — `<role>`

Public interfaces:
- `<type/class/function>` — `<purpose>`

## Data model

Entities:
- `<Entity>`: `<fields, invariants>`

Relationships:
- `<A ↔ B>`

Migrations required:
- `<migration name>` — `<what changes>`

## Boundaries

| Boundary | Adapter | Contract |
|----------|---------|----------|
| HTTP consumer → `<service>` | `<class>` | `contracts/<file>` |
| Queue producer → `<topic>` | `<class>` | `contracts/<file>` |
| Clock | `<interface>` | n/a |
| Randomness | `<interface>` | n/a |

## Error model

- `<ErrorType>`: `<when thrown, how surfaced>`
- `<…>`

## Observability

- Logs: `<what, at which level, with which correlation id>`
- Metrics: `<counter/gauge/histogram names>`
- Traces: `<spans, propagation>`

## Security

- Authn: `<mechanism>`
- Authz: `<policy>`
- Input validation: `<where, how>`
- Secrets handling: `<source, rotation, logs redaction>`
- ADRs touched: `<list>`

## Test strategy

- Unit: `<which modules; target coverage>`
- Contract: `<which boundaries; provider verification?>`
- Integration: `<hermetic environment; compose/testcontainers>`
- E2E: `<scope, tooling; optional>`

## Rollout (if applicable)

- Feature flag: `<name, default>`
- Migration order: `<steps>`
- Compatibility windows: `<versions>`
- Rollback: `<plan>`

## References

- Spec: `./spec.md`
- Research: `./research.md`
- Contracts: `./contracts/`
- ADRs: `../adr/`
