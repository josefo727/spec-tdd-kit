# Example — Greenfield, minimal walkthrough

A small API that returns whether you should take an umbrella today, based on a probability threshold. One vertical slice, end-to-end.

Stack: Node.js + Fastify + Vitest + Zod. Substitute per your stack file.

---

## Phase 0 — Bootstrap

```bash
git init
mkdir -p .specs/adr
npm init -y
pnpm add -D vitest typescript tsx @types/node
pnpm add fastify zod
```

`package.json` scripts: `"test": "vitest run"`.

Commit: `chore: init repository`.

---

## Phase 1 — Constitution

Run `/sdd-constitution`. Output (abbreviated):

```
# Project Constitution — umbrella-api

## Articles
I   Spec-anchored development.
II  Test-first.
III Boundary-only mocks (HTTP out only).
IV  Contract-first integration (Open-Meteo).
V   No silent clarification.
VI  ADR for non-local decisions.
VII Boundaries: HTTP out via fetch through WeatherClient; Clock via Clock interface.

## Optional adopted
- Type safety: no `any` in exports.
- Coverage floor: 85% domain/.
```

Commit: `spec: initial constitution v1`.

---

## Phase 2 — Specify

Run `/sdd-specify umbrella-check`. After one round of `AskUserQuestion`:

```
# Spec — 001-umbrella-check

## User story
As a commuter, I want to query whether to take an umbrella today, so that I stop checking the forecast manually.

## Acceptance criteria
1. GET /umbrella?lat=<n>&lon=<n> returns JSON { needsUmbrella: boolean, maxProbability: number, rainyHours: string[] }.
2. Coordinates outside ±90 / ±180 return HTTP 400 with body { error: "invalid_coords" }.
3. When the upstream forecast API fails, the endpoint returns HTTP 503 with body { error: "upstream_unavailable" }.
4. The threshold is 30 by default; env var UMBRELLA_THRESHOLD overrides within [0..100].
5. The endpoint responds in under 300ms at p95 under cached forecasts.

## Non-goals
- Persisting user preferences.
- Authentication.
```

No `[NEEDS CLARIFICATION]` remain after `/sdd-clarify`. Commit: `spec: 001 clarifications resolved`.

---

## Phase 3 — Plan

`/sdd-plan`. Excerpts:

`research.md`:

```
## open-meteo@forecast.v1
- captured: 2026-04-24
- source: context7 (library-id: open-meteo)
- Endpoint: GET https://api.open-meteo.com/v1/forecast
- Required: latitude, longitude, hourly=precipitation_probability
- No API key; rate limit ~10k/day per IP
- Decision impact: WeatherClient wraps this endpoint; contract in contracts/open-meteo.yaml
```

`contracts/open-meteo.yaml`: (see `templates/contracts.md` pattern).

`plan.md`:

- Stack: Node 22 / TS 5.6 / Fastify 5 / Zod / Vitest 3.
- Layout: `src/domain/`, `src/application/`, `src/infra/http/`, `src/infra/weather/`, `src/server.ts`.
- Boundaries: `WeatherClient` (HTTP), `SystemClock`.
- Test strategy: unit (domain), contract (WeatherClient against `contracts/open-meteo.yaml`), integration (Fastify `inject()`).
- No rollout concerns (fresh service).

Commit: `plan: 001 initial plan + research + contracts`.

---

## Phase 4 — Tasks

`/sdd-tasks`. Excerpt of `tasks.md`:

```
T001  Scaffold Vitest + TS strict
T002  Scaffold Fastify app with /health
T003  Contract test: WeatherClient GET /forecast
T004  Implement WeatherClient → green T003
T005  Domain: shouldTakeUmbrella(probs, threshold) pure function
T006  Application: UmbrellaService using WeatherClient + SystemClock
T007  HTTP handler GET /umbrella (validates coords)
T008  Error mapping: invalid_coords / upstream_unavailable
T009  [P] Config loader with UMBRELLA_THRESHOLD validation
T010  p95 budget smoke — ignored if env var UMBRELLA_SKIP_PERF
```

---

## Phase 5 — Implement (one task shown)

### T005 — Domain `shouldTakeUmbrella`

#### R — `/tdd-red`

Write `tests/unit/shouldTakeUmbrella.test.ts`:

```ts
import { describe, it, expect } from 'vitest'
import { shouldTakeUmbrella } from '@/domain/umbrella'

describe('shouldTakeUmbrella', () => {
  it('returns needsUmbrella true when any probability >= threshold', () => {
    const r = shouldTakeUmbrella(
      { time: ['08:00', '09:00', '10:00'], probs: [10, 45, 20] },
      30,
    )
    expect(r.needsUmbrella).toBe(true)
    expect(r.maxProbability).toBe(45)
    expect(r.rainyHours).toEqual(['09:00'])
  })
})
```

Run — fails with "Cannot find module '@/domain/umbrella'". Create the stub:

```ts
// src/domain/umbrella.ts
export function shouldTakeUmbrella(_data: unknown, _threshold: number): never {
  throw new Error('NotImplemented')
}
```

Re-run — now fails with `Error: NotImplemented` (semantic failure). Commit:

```
test: T005 red — shouldTakeUmbrella returns needsUmbrella=true at threshold=30

Observed failure:
  FAIL tests/unit/shouldTakeUmbrella.test.ts
  ● shouldTakeUmbrella returns needsUmbrella true ...
  Error: NotImplemented

spec-ref: §Acceptance 4 (threshold default 30; T005 covers pure predicate)
```

#### G — `/tdd-green`

Minimal implementation:

```ts
export type Forecast = { time: string[]; probs: number[] }
export type UmbrellaResult = {
  needsUmbrella: boolean
  maxProbability: number
  rainyHours: string[]
}

export function shouldTakeUmbrella(f: Forecast, threshold: number): UmbrellaResult {
  const rainy = f.probs.map((p, i) => ({ time: f.time[i], p })).filter(x => x.p >= threshold)
  return {
    needsUmbrella: rainy.length > 0,
    maxProbability: Math.max(0, ...f.probs),
    rainyHours: rainy.map(x => x.time),
  }
}
```

Run suite — green. Commit: `feat: T005 green — implement shouldTakeUmbrella`.

#### F — `/tdd-refactor`

Extract `buildRainyHours` for clarity; add a property test using `fast-check`:

```ts
import fc from 'fast-check'

it('maxProbability is never less than any input probability', () => {
  fc.assert(
    fc.property(fc.array(fc.integer({ min: 0, max: 100 })), (probs) => {
      const r = shouldTakeUmbrella({ time: probs.map(String), probs }, 30)
      return probs.every(p => p <= r.maxProbability) || probs.length === 0
    }),
  )
})
```

Commit: `refactor: T005 — extract buildRainyHours; add maxProbability property test`.

Update `tasks.md`: `T005` status closed, three SHAs recorded.

---

## Phase 6 — Verify

`/sdd-verify` output:

```
# Verify report — 001-umbrella-check
- R1 Spec coverage: PASS (5/5)
- R2 Task completeness: PASS (10/10)
- R3 Orphan tests: PASS
- R4 Contracts: PASS (contracts/open-meteo.yaml v1.0.0)
- R5 Constitution: PASS (I–VII)
- R6 Research freshness: PASS
- R7 Observability: N/A
- R8 Security: N/A
- R9 Docs: WARN (README missing usage)
- R10 Changelog: PASS
```

Fix R9 by adding a Usage section. Re-run: green. Commit: `spec: 001 closed — verify green`.

---

## Takeaways

- Under a half-day for a vertical slice with one external boundary.
- Three commits per task force explicit authorship of tests, code, and refactor.
- The constitution's "Article III (boundary-only mocks)" means the domain test in T005 uses no mocks at all; `WeatherClient` is the only mocked piece, and its boundary is tested via contract, not imagined.

## References

- Greenfield workflow: [`../workflows/greenfield.md`](../workflows/greenfield.md)
- Fusion rules: [`../philosophy/03-sdd-tdd-fusion.md`](../philosophy/03-sdd-tdd-fusion.md)
