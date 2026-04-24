# Stack — Vue

## Defaults

- **Version:** Vue 3.x with Composition API + `<script setup>`.
- **Build:** Vite 5.x+.
- **Test runner:** Vitest 3.x.
- **Component testing:** `@testing-library/vue` (behavior) + `@vue/test-utils` (wiring).
- **E2E:** Playwright (preferred) or Cypress.
- **Type check:** TypeScript 5.x + `vue-tsc`.
- **State:** Pinia.
- **Property-based:** `fast-check`.

## Folder layout

```
src/
  modules/
    <feature>/
      components/
      composables/
      stores/
      services/      # boundary adapters
      pages/
  lib/               # generic utilities, pure functions
tests/
  unit/
  component/
  contract/
  integration/
  e2e/
```

## Testing patterns

### Pure composables

Composables with no DOM interaction live in `composables/` and are tested as pure functions in `tests/unit/`.

### Components

Use Testing Library for behavior-first assertions:

```ts
import { render, screen } from '@testing-library/vue'
import userEvent from '@testing-library/user-event'
import UmbrellaWidget from '@/modules/weather/components/UmbrellaWidget.vue'

test('shows "take umbrella" when probability >= threshold', async () => {
  render(UmbrellaWidget, { props: { probability: 45, threshold: 30 } })
  expect(screen.getByRole('status')).toHaveTextContent(/take umbrella/i)
})
```

Avoid asserting on internal structure (child components, specific classes) unless it is the observable under test.

### Stores (Pinia)

Test stores with a fresh Pinia instance per test. Assert on state transitions triggered by actions, not on how actions are implemented.

### Services / HTTP

Boundary. Mock at `fetch`/`axios` level using MSW (`msw`) for contract-aware mocks. Write contract tests against the same MSW handlers.

### E2E

Playwright scenarios tied 1:1 to acceptance criteria. Keep under 10 per feature; move rare paths to component-level integration.

## Boundaries

- HTTP: one `services/*.ts` per endpoint group. Inject via composable or Pinia action. Mock via MSW.
- Clock: `Clock` interface in `lib/clock.ts`; prod uses `Date.now`; tests use a fake.
- Randomness: `Random` interface; seed in tests.
- Router: Vue Router; mock `useRouter` / `useRoute` in component tests when needed.

## Linting and types

- ESLint + `@typescript-eslint` + `eslint-plugin-vue`.
- `vue-tsc --noEmit` in CI.
- No `any` in exported types.

## Plan decisions (typical)

- Feature-sliced layout under `src/modules/`.
- Stores own async state; components stay presentational.
- Accessibility: prefer semantic HTML and ARIA only where needed.
- SSR/SSG: decide explicitly; Nuxt is a different stack file.

## Subagent

`vue-developer` for component scaffolding, Composition API migrations, Pinia store patterns. See [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md).
