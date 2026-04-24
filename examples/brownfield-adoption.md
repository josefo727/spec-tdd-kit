# Example — Brownfield adoption

Adopting the kit on an existing Laravel 11 monolith. The team has 40% coverage, mostly Feature tests, Pest. No `.specs/`. Goal: bring the kit in, add one new feature under SDD+TDD, without rewriting the past.

---

## Phase 0 — Intake

`/sdd-onboard` runs. Intake recorded to `.specs/onboarding.md`:

```
## Intake
- Language: PHP 8.3
- Framework: Laravel 11.14
- Package manager: Composer 2.8
- Test runner: Pest 3.5 (classic + PHPUnit compat)
- Test command: composer test
- Tests live in: tests/Feature, tests/Unit
- CI: GitHub Actions (matrix php 8.2 + 8.3; sqlite in-memory)
- Deployment: Forge + Laravel Vapor for queues
- LOC: 180k PHP, 210 files under app/
- Pain points: flaky feature tests, no contract tests, HTTP mocks scattered
```

---

## Phase 1 — Discovery

`Explore` subagent returns a one-page report. Key findings:

- Clean Controllers → Actions → Services → Eloquent Models.
- `app/Domain/` exists but imports `Illuminate\Support\Str` in a few places (Article III violation candidate).
- `App\Services\Http\HttpClient` wraps `Http::` for outbound requests; tests stub with `Http::fake()`.
- Queue via Redis; `Queue::fake()` used.
- Clock usage inconsistent: `now()` sprinkled across domain.
- 19 flaky feature tests identified by CI history.

---

## Phase 2 — Constitution extraction

`/sdd-onboard` proposes `.specs/constitution.md`:

```
## Preamble
This application serves SMB retailers. Reliability and auditability are paramount.

## Current (de facto)
- Article A — Controllers are thin; orchestration lives in Actions (evidence: app/Http/Controllers/OrderController.php)
- Article B — Outbound HTTP goes through App\Services\Http\HttpClient (evidence: app/Services/Http/HttpClient.php)
- Article C — Tests use Pest functional syntax (evidence: tests/Feature/OrdersTest.php)

## Aspirational
- Article I — Spec-anchored development (applies_from: today; migration: new features only)
- Article II — Test-first (applies_from: today; touch-fix existing)
- Article III — Boundary-only mocks. Boundaries: HttpClient, Queue, Mail, Event, Clock (CarbonImmutable via Clock binding), Random.
  (applies_from: today; touch-fix: when we edit a test file using a non-boundary mock, migrate it)
- Article IV — Contract-first integration (applies_from: today)
- Article V — No silent clarification
- Article VI — ADR for non-local decisions

## Amendments
(empty)
```

User approves. Commit: `spec: brownfield constitution extracted (v1 of repo)`.

---

## Phase 3 — Test inventory

`.specs/test-inventory.md` (abbrev):

```
## Summary
- Total tests: 612 (Unit 221, Feature 391)
- Coverage: line 41%, branch 33%
- Slow (>500ms): 58 tests in Feature/Orders, Feature/Reports
- Flaky (CI history): 19 tests (race conditions on queue assertions)

## Smells
- tests/Unit/Services/* mocks Eloquent directly (violates future Article III via boundary-only mocks)
- tests/Feature/Orders/* shares $order fixture across 40 tests (brittle)

## Touch-fix backlog (address when editing)
- 19 flaky tests
- 8 tests mocking Eloquent directly
```

---

## Phase 4 — Brownfield index

`.specs/index.md`:

```
## Modules under SDD+TDD
- (empty)

## Modules under legacy rules
- app/* except where SDD+TDD listed above

## Boundaries (declared)
- HttpClient: App\Services\Http\HttpClient
- Queue: Illuminate\Contracts\Queue\Factory (Laravel)
- Mail: Illuminate\Contracts\Mail\Mailer
- Event: Illuminate\Contracts\Events\Dispatcher
- Clock: App\Support\Clock (to be created per Article III migration)
- Randomness: App\Support\Random (to be created)
```

Commit: `spec: brownfield inventory + index`.

---

## Phase 5 — First kit-managed feature

Feature: **"Refund eligibility check"** — an endpoint to determine if an order can be refunded, given business rules.

### Specify / Clarify

`spec.md` drafted via `/sdd-specify`. Two markers emerge:

- `[NEEDS CLARIFICATION: partial refunds allowed, or only full?]` → answered "full only, for now".
- `[NEEDS CLARIFICATION: eligibility window — 30 or 60 days?]` → answered "30 days, matches policy".

### Plan

`/sdd-plan`. `context7` queried for `spatie/laravel-package-tools` (not used), `nesbot/carbon` (Carbon Immutable docs), no third-party API for this feature. Plan picks:

- Pure domain module `app/Domain/Refund/RefundEligibility.php`.
- Action `app/Actions/Orders/CheckRefundEligibility.php`.
- Controller method on existing `OrderController`.
- No new migration; uses existing `orders` table timestamps.
- Clock injected via new `App\Support\Clock` (Article III migration by touch-fix).

Contracts:

- Internal: a TypeScript-style interface file in `contracts/refund-eligibility.yaml` describing the Action's input/output shape (this project uses YAML for both HTTP and internal contracts).

### Tasks

`tasks.md`:

```
T001  Add App\Support\Clock + SystemClock binding (Article III migration)
T002  Contract test for Action input/output against contracts/refund-eligibility.yaml
T003  Pure domain: RefundEligibility::decide(order, now) returns decision
T004  Action: CheckRefundEligibility (wires Clock + repository)
T005  Controller: POST /orders/{id}/refund-eligibility returns decision
T006  Feature test: happy path, expired, already-refunded
T007  [P] Update README with feature docs
```

### Implement (highlights)

- **T001 Red**: test asserts `Clock::now()` is injectable and defaults to `CarbonImmutable::now()` — currently fails because `App\Support\Clock` does not exist.
- **T001 Green**: create `App\Support\Clock` class, binding in `AppServiceProvider`.
- **T003 Red**: pure test, no Laravel; `RefundEligibility::decide` not defined.
- **T003 Green**: minimal if-chain.
- **T003 Refactor**: extract `isWithinWindow`, add property tests on boundary dates.
- **T006** uses `Http::fake()` — no calls here, but enforces no accidental outbound.

### Verify

`/sdd-verify` passes R1–R10. Constitution Article III is honored in all new code; `tests/Unit/Refund/*` uses no mocks.

`spec.md` closed. `.specs/index.md` updated:

```
## Modules under SDD+TDD
- app/Domain/Refund/**
- app/Actions/Orders/CheckRefundEligibility
- app/Support/Clock (added during migration)
```

Commit: `spec: 001 closed — verify green`.

---

## Retro observations

- The hardest part was not the new feature; it was resisting the urge to rewrite the legacy `Refund*` code. The kit prevented it.
- The Clock migration paid off immediately: one bug surfaced in an old test that was time-dependent; it was quarantined for touch-fix later.
- Coverage rose from 41% to 43%; incremental, which is correct.

---

## Takeaways

- Brownfield is not SDD-on-the-whole-repo. It is SDD-on-the-next-touch.
- Aspirational articles let the kit coexist with years of legacy code.
- Touch-fix migration is slower than big-bang but survives. Big-bang dies in review.

## References

- Brownfield workflow: [`../workflows/brownfield.md`](../workflows/brownfield.md)
- Feature flow: [`../workflows/feature-flow.md`](../workflows/feature-flow.md)
- Anti-pattern: [`../anti-patterns.md#14-brownfield-constitution-invented-not-extracted`](../anti-patterns.md)
