# Stack — Laravel / PHP

## Defaults

- **Language:** PHP 8.3+.
- **Framework:** Laravel 11.x.
- **Test runner:** Pest 3.x (preferred) or PHPUnit 11.x.
- **Type check:** PHPStan level 8 or Psalm level 3.
- **Lint / style:** Laravel Pint (configured for `per-cs-fixer`).
- **Property-based:** `eris` for property testing where warranted.
- **HTTP mocks:** `Illuminate\Http\Client\Factory::fake()` at the boundary.

## Spatie guidelines

Always use the `php-guidelines-from-spatie` skill installed at `~/.claude/skills/php-guidelines-from-spatie/`. It enforces Spatie's PHP and Laravel conventions (naming, typed properties, avoiding shorthand, etc.).

Invoke when:
- writing new classes,
- refactoring during F,
- reviewing third-party code coming into the repo.

## Folder layout (override)

```
app/
  Domain/               # pure domain (no Laravel facades)
  Application/          # use-cases, orchestrators
  Infrastructure/       # boundary adapters (HTTP, DB, queue)
  Http/
  Console/
tests/
  Unit/                 # Domain + Application
  Feature/              # Through Laravel kernel, hermetic DB
  Contract/
  Integration/          # Real external services via testcontainers / sqlite:memory
```

Keep `Domain/` free of `Illuminate\*` imports. Enforce with an architecture test (`pestphp/pest-plugin-arch`).

## Testing patterns

### Pest preference

Use Pest's functional syntax for readability:

```php
it('returns needsUmbrella=true at threshold=30', function () {
    $result = shouldTakeUmbrella([30, 10, 5], threshold: 30);

    expect($result->needsUmbrella)->toBeTrue();
});
```

### Feature tests

Feature tests boot the Laravel kernel. Use:

- `RefreshDatabase` on SQLite in-memory for speed, or real DB when behavior depends on engine specifics (Postgres JSON ops, MySQL locking).
- `Http::fake()` for outbound HTTP.
- `Queue::fake()`, `Event::fake()`, `Mail::fake()` for boundary assertions.

### Contract tests

Place under `tests/Contract/`. Use `Http::fake` with an `assertSent` that validates request shape against the contract file in `contracts/`.

### Architecture tests (Pest-Arch)

Declare and run:

```php
arch('domain is free of Illuminate')
    ->expect('App\\Domain')
    ->not->toUse('Illuminate');
```

### Data model tests

Use factories. Every Eloquent model has a `HasFactory` with explicit defaults. Tests build domain objects via factories, not via `new`.

## Boundaries

- HTTP out: `Http::` facade; mock with `Http::fake()`.
- Database: Eloquent; use in-memory SQLite for hermetic tests; real engine for engine-dependent features.
- Queue: `Queue::fake()`; assert jobs dispatched.
- Events: `Event::fake()`; assert events fired.
- Mail: `Mail::fake()`.
- Clock: inject `Illuminate\Support\Carbon` provider or a custom `Clock` interface; prefer `CarbonImmutable::now()` through a container-bound clock.
- Randomness: bind a `Random` interface; in tests, seed it.

## Linting and types

- `vendor/bin/pint --test` in CI.
- `vendor/bin/phpstan analyse --level=8`.
- Baseline file only for pre-existing issues; new code must be clean.

## Plan decisions (typical)

- Controllers thin; use FormRequest validation; delegate to Application layer.
- Services in `Application/`; entities and value objects in `Domain/`.
- Queue jobs live in `Infrastructure/Queue/`.
- API responses via `Resource` classes; never leak Eloquent models.
- Migrations reversible; add a `down()` that works.

## Commit examples

```
test: T042 red — shouldTakeUmbrella returns true at threshold=30
feat: T042 green — add shouldTakeUmbrella in App\Domain\Umbrella
refactor: T042 — extract precipitation normalization
```

## Subagents

- Feature building: `laravel-feature-builder`
- Debugging: `laravel-debugger`
- Simplification: `laravel-simplifier`

See [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md).
