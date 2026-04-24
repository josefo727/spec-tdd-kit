# Stack — React

## Defaults

- **Version:** React 19.x.
- **Build:** Vite 5.x+ (or Next.js if app-router required).
- **Test runner:** Vitest 3.x (Jest 30.x acceptable if legacy).
- **Component testing:** `@testing-library/react` + `@testing-library/user-event`.
- **E2E:** Playwright.
- **Type check:** TypeScript 5.x.
- **State:** Zustand or Redux Toolkit; choose per feature shape.
- **HTTP mocks:** MSW.

## Folder layout

```
src/
  features/
    <feature>/
      components/
      hooks/
      stores/
      services/
      routes/
  lib/
tests/
  unit/
  component/
  contract/
  integration/
  e2e/
```

## Testing patterns

### Hooks

Pure hooks (no DOM, no fetch) live under `hooks/` and are tested with `renderHook`:

```ts
import { renderHook, act } from '@testing-library/react'
import { useUmbrellaThreshold } from '@/features/weather/hooks/useUmbrellaThreshold'

test('updates threshold within bounds', () => {
  const { result } = renderHook(() => useUmbrellaThreshold({ initial: 30 }))
  act(() => result.current.set(50))
  expect(result.current.value).toBe(50)
})
```

### Components

Behavior-first:

```tsx
render(<UmbrellaWidget probability={45} threshold={30} />)
expect(screen.getByRole('status')).toHaveTextContent(/take umbrella/i)
```

Do not snapshot. Snapshots are tautology in disguise unless used on stable, small outputs.

### Data fetching

Prefer a service layer (`services/weather.ts`) that owns `fetch`/`axios`. Components call through hooks. Mock at the service layer via MSW; write contract tests that share MSW handlers with prod-like fixtures.

### State

- **Zustand**: test stores by creating a fresh store instance per test.
- **Redux Toolkit**: test slices as pure reducers; test thunks by dispatching and inspecting state.

### E2E

Playwright. One scenario per acceptance criterion. Use test IDs sparingly; prefer accessible queries.

## Boundaries

- HTTP: `services/*.ts`.
- Clock / Randomness: inject via context or explicit prop.
- Router: `react-router` or Next router; mock at the provider level.

## Linting and types

- ESLint + `@typescript-eslint` + `eslint-plugin-react-hooks`.
- `tsc --noEmit`.
- No `any` in exported types.

## Plan decisions (typical)

- Use React Server Components only when the framework supports them; otherwise SPA.
- Suspense + Error Boundaries for loading/error states.
- Accessibility: semantic HTML + ARIA where needed; axe-core in CI.

## Subagent

None React-specific included; use generalist agents + `frontend-design` skill when UI polish is the task.
