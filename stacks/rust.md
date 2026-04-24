# Stack — Rust

## Defaults

- **Edition:** 2024 (or 2021 where toolchain dictates).
- **Toolchain:** `rustup` stable; `cargo` as build and test driver.
- **Test runner:** built-in `cargo test`, plus `cargo nextest` for large suites.
- **Property-based:** `proptest` or `quickcheck`.
- **Async runtime:** Tokio (preferred) or `async-std` if niche.
- **HTTP client:** `reqwest` (tokio) or `hyper` directly.
- **Web framework:** `axum` (preferred), `actix-web`, or `rocket`.
- **DB:** `sqlx` (preferred, async) or `diesel` (sync).
- **Lint / format:** `cargo clippy --all-targets --all-features -D warnings` + `cargo fmt --check`.

## Folder layout

```
crates/
  domain/           # pure; no tokio, no sqlx
  application/
  infra/
tests/              # integration tests (cargo convention)
```

In a single-crate layout, use `src/` with modules and `tests/` at root for integration tests.

## Testing patterns

### Unit tests

Inline `#[cfg(test)] mod tests { … }` per module. Keep fast; no IO.

### Integration tests

Under `tests/*.rs`. One binary per file by Cargo convention. Spin real services with `testcontainers-rs`.

### Property-based

```rust
proptest! {
    #[test]
    fn umbrella_never_panics(probs in prop::collection::vec(0..=100u8, 0..=24)) {
        let _ = should_take_umbrella(&probs, 30);
    }
}
```

### HTTP

- Consumer tests: `mockito` or `wiremock` for out-of-process mocking.
- `axum` provides `Router::oneshot` / `TestClient` for in-process tests.

### Async

Use `#[tokio::test]`. Avoid `block_on` in tests.

### Contract tests

Serde-serialized fixtures validated against OpenAPI (`utoipa`) or JSON schemas in `contracts/`.

## Boundaries

- HTTP: `reqwest::Client` injected as a trait object or generic.
- DB: `sqlx::PgPool` injected via `AppState`.
- Clock: `Clock` trait; prod uses system; tests use `FixedClock`.
- Randomness: `rand::RngCore` injected; tests use `StepRng` or seeded SmallRng.

## Linting and types

- `cargo clippy --all-targets --all-features -D warnings`.
- `cargo fmt --check`.
- `cargo deny` for licenses/security advisories.

## Plan decisions (typical)

- Error model: `thiserror` in libs, `anyhow` in bins, `color-eyre` in examples.
- Logging: `tracing` with `tracing-subscriber`; JSON in prod, human in dev.
- Config: `figment` or `config` + env overrides.
- Observability: OpenTelemetry via `tracing-opentelemetry`.

## Performance

- `cargo bench` with `criterion` for hot paths.
- Budgets in `plan.md`; ignore micro-regressions; flag macro-regressions.
