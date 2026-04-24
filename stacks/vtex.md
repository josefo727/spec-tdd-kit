# Stack — VTEX

VTEX spans multiple runtimes: **VTEX IO** (apps, React + Node resolvers), **FastStore** (Next.js + GraphQL), **Admin / Master Data**, and **CMS**. Testing practice differs by runtime.

## Defaults

- **VTEX IO apps (React):** React 17/18 + VTEX design system; tests with `@vtex/test-tools` + Jest 29.x or Vitest 3.x.
- **VTEX IO service resolvers (Node):** TypeScript; `@vtex/api`; unit tests with Vitest.
- **FastStore:** Next.js 14.x; Vitest + Testing Library; Playwright for e2e.
- **Admin / Master Data:** REST/GraphQL clients; contract tests against VTEX APIs.

## Folder layout (IO app)

```
react/
  components/
  modules/          # pure logic
node/
  resolvers/
  clients/          # VTEX IO clients (Catalog, Search, …)
messages/           # i18n
store/              # store theme blocks (if store-theme-like)
tests/
  react/
  node/
  contract/
  integration/
```

## Testing patterns

### React components (IO)

```ts
import { render, screen } from '@vtex/test-tools/react'
```

`@vtex/test-tools` provides VTEX context mocks. Avoid mocking VTEX hooks one-by-one; use the provided helpers.

### Node resolvers (IO)

- Pure resolvers tested with fake `IOContext`.
- Use dependency injection: pass clients into the resolver; tests pass fake clients.
- Do not hit real VTEX APIs in unit tests.

### Contract tests

VTEX APIs evolve. For every client used (Catalog, Search, OMS, Master Data, Pricing, Orders), maintain a contract file in `contracts/vtex/<api>.yaml` with the subset of fields consumed. Scheduled contract tests hit real VTEX endpoints in a sandbox account weekly.

### FastStore

Treat as Next.js app. Co-locate tests; Playwright for critical paths. GraphQL schema generation step must be deterministic; commit generated types to detect drift.

### Master Data

- Unit tests: in-memory fake MD client.
- Integration: run against a sandbox MD schema; snapshot response shapes.

### Store theme / CMS blocks

- Test the underlying React modules as React components.
- Storefront rendering is validated via visual regression (Percy, Chromatic) or Playwright screenshots; do not rely solely on this.

## Boundaries

- VTEX clients (Catalog, Search, OMS, …): inject via resolver params; fake in tests.
- HTTP out: any third-party integration through a dedicated client class.
- Clock / Randomness: inject; seed in tests.

## Linting and types

- ESLint with VTEX's ESLint config.
- `tsc --noEmit`.
- GraphQL schema validation as part of CI for FastStore.

## Plan decisions (typical)

- Runtime split (IO vs. FastStore vs. custom service): declared in plan.
- Sandbox/workspace strategy: one workspace per feature; link to contract checks.
- Billing-visible side effects (orders, pricing, inventory): extra review gate; add security-review per constitution.

## VTEX-specific rollout

- Workspace link → promote → publish → install. Each stage has rollback cost. Plan tests per stage:
  - Unit + contract before link.
  - Integration on linked workspace.
  - Smoke e2e on a production-like workspace before publishing.

## Research

Use `context7` for VTEX IO docs. VTEX docs evolve fast; `captured` dates matter more than average.
