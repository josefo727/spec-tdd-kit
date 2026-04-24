# Stack — Flutter / Dart

## Defaults

- **Flutter:** stable channel (≥3.22).
- **Dart:** 3.5+.
- **State management:** `flutter_riverpod` (preferred) or `provider`.
- **Routing:** `go_router`.
- **Networking:** `dio` or `http` + `retrofit`.
- **Test runners:** `flutter_test` (widget/unit), `integration_test` (e2e).
- **Mocks:** `mocktail` (preferred — no codegen) or `mockito`.
- **Property-based:** `glados`.
- **Lint:** `very_good_analysis` or `flutter_lints`.

## Folder layout

```
lib/
  features/
    <feature>/
      data/           # repositories, datasources (boundary)
      domain/         # entities, pure use-cases
      presentation/   # widgets, controllers
  core/               # shared infra (clock, random, env)
test/
  unit/
  widget/
  integration_test/   # at project root per Flutter convention
```

## Testing patterns

### Domain

Pure Dart tests under `test/unit/`. No Flutter imports. Fast.

### Widgets

```dart
testWidgets('shows take umbrella at threshold', (tester) async {
  await tester.pumpWidget(const UmbrellaApp(probability: 45, threshold: 30));
  expect(find.text('Take umbrella'), findsOneWidget);
});
```

Prefer finders by semantics (`find.bySemanticsLabel`) where accessibility matters.

### Integration tests

Use `integration_test` package. Drive real app on emulator/real device. Keep to acceptance criteria.

### Golden tests

Optional. Useful for stable, design-reviewed widgets. Do not use as a tautology substitute for behavior tests.

### HTTP

Mock at the `dio`/`http.Client` level. `mocktail` + fake response data.

### Contract tests

JSON schemas in `contracts/`; Dart model classes (`freezed`/`json_serializable`) decode per schema; round-trip tests catch drift.

## Boundaries

- HTTP: `dio` instance behind a repository interface.
- DB / local storage: `drift` (SQL) or `sembast` (NoSQL) behind a repository.
- Clock: `Clock` class from `clock` package; `withClock(FakeClock(...), () { ... })` in tests.
- Randomness: `Random` injected; seed in tests.

## Linting and types

- `dart analyze` with the chosen ruleset as error-level.
- `dart format --set-exit-if-changed .` in CI.
- Avoid `dynamic`; use sealed classes + pattern matching for sum types.

## Plan decisions (typical)

- Architecture: layered (`data/domain/presentation`) or feature-sliced.
- DI via Riverpod providers; avoid singletons.
- Error model: sealed classes `Result<T, E>` for fallible operations.
- Localization: `intl`; plan copy deck from spec.

## Performance

- `flutter analyze`, `flutter test --coverage`.
- Profile with DevTools; budgets in plan when user-visible.
