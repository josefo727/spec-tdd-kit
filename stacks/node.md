# Stack — Node.js

## Defaults

- **Runtime:** Node.js 22 LTS (or 20 LTS for conservative targets). Consider Bun 1.x for new services if the constitution allows.
- **Language:** TypeScript 5.x.
- **Test runner:** Vitest 3.x. `node --test` acceptable for zero-dep libraries.
- **HTTP:** Fastify (preferred for API services) or Express. Hono for edge-first.
- **DB:** Drizzle ORM or Prisma; raw SQL via `postgres.js` when performance critical.
- **Property-based:** `fast-check`.

## Folder layout

```
src/
  domain/
  application/
  infra/
    http/
    db/
    queue/
    clock.ts
    random.ts
  server.ts
tests/
  unit/
  contract/
  integration/
  e2e/
```

Keep `domain/` dependency-free.

## Testing patterns

### Domain

Pure unit tests, no framework.

### Application

Compose domain with in-memory fakes for boundaries. Fakes live in `tests/fakes/`.

### HTTP layer

- Integration tests spawn the app on a random port (Fastify's `app.inject()` avoids the port entirely).
- Assert on HTTP responses, not on controller internals.

### Database

Two layers:

- In-memory or SQLite fake for fast tests where SQL differences do not matter.
- Real Postgres via `testcontainers` for tests that depend on engine specifics (JSON ops, locks).

### Queue

Boundary. Use in-memory fake queue for unit/integration. Contract test against real broker in CI (nightly).

### Contract tests

`zod` or `ajv` schemas shared between code and contract tests. Contract test = "our request/response validates against the schema in `contracts/`".

## Boundaries

- HTTP out: `fetch` (native) or `undici`. Mock with `undici`'s `MockAgent` or MSW-Node.
- DB: the chosen ORM/driver.
- Queue: client lib.
- Clock / Randomness: interfaces injected via the app factory.

## Linting and types

- ESLint + `@typescript-eslint`.
- `tsc --noEmit` in CI.
- Strict `tsconfig`: `strict: true`, `noUncheckedIndexedAccess: true`, `exactOptionalPropertyTypes: true`.

## Plan decisions (typical)

- Single app or monorepo (pnpm workspaces, Turbo, Nx). Declare in ADR.
- Logging via `pino`; correlate via async_hooks / AsyncLocalStorage.
- Config via `zod`-validated env loader.

## Performance tests (optional)

- `autocannon` or `k6` for load.
- Budgets in `plan.md`.
