# Stack — Quasar

## Defaults

- **Quasar:** v2.x on Vue 3.
- **Build modes:** SPA, SSR, PWA, Mobile (Cordova/Capacitor), Desktop (Electron). Pick in plan; each mode has testing caveats.
- **Language:** TypeScript 5.x.
- **Test runner:** Vitest 3.x for unit/component.
- **Component testing:** `@vue/test-utils` + `@testing-library/vue`.
- **E2E:** Cypress (Quasar integrates out-of-the-box) or Playwright.
- **State:** Pinia.
- **HTTP:** `axios` (Quasar default) or `fetch`.

## Folder layout

```
src/
  pages/
  layouts/
  components/
  stores/
  services/
  composables/
  boot/               # Quasar boot files
test/
  vitest/
  cypress/            # when Cypress chosen
```

## Testing patterns

### Quasar plugins

Quasar components need `installQuasar` in unit tests:

```ts
import { installQuasarPlugin } from '@quasar/quasar-app-extension-testing-unit-vitest/options'

installQuasarPlugin({ plugins: { Dialog: {}, Notify: {} } })
```

### Components

Behavior-first. For Quasar UI components, assert via user-visible output, not Quasar internals.

### Boot files

Boot files wire Quasar-level behavior (auth, axios interceptors). Keep them thin. Test the underlying logic in isolation; boot files are integration-tested via a minimal bootstrapped app.

### Axios

Intercept with MSW or with a shared axios instance that tests can `axios.mockResolvedValue`.

### Capacitor / Cordova modes

- Mock native plugins in tests via interface adapters.
- Real device e2e only in nightly pipelines.

### SSR mode

Add tests for hydration (component mounts match server output). Use Quasar's `vitest` plugin configuration for SSR.

## Boundaries

- HTTP: `services/*.ts` using Quasar-configured axios.
- Native: when in Capacitor/Cordova mode, wrap native APIs in interfaces.
- Clock / Randomness: same as Vue stack.

## Linting and types

- Quasar CLI includes ESLint setup; enable `@typescript-eslint/strict` rules.
- `vue-tsc --noEmit` in CI.

## Plan decisions (typical)

- Mode (SPA vs. SSR vs. Mobile vs. Electron): decided in plan; re-mode requires ADR.
- Platform-specific behavior gated by `Platform.is.*`; test both branches.
- Icons: Material Icons or `mdi` via Quasar's icon plugin; decision in plan.

## See also

- [`./vue.md`](./vue.md) — most Vue patterns carry over.
