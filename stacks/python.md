# Stack — Python

## Defaults

- **Version:** Python 3.12+.
- **Packaging:** `uv` (preferred) or `pip-tools`.
- **Project layout:** `pyproject.toml`.
- **Test runner:** `pytest` 8.x+.
- **Type check:** `mypy` strict (or `pyright` strict).
- **Lint / format:** `ruff` (lint + format).
- **Property-based:** `hypothesis`.
- **HTTP client:** `httpx`.
- **Web framework:** FastAPI (APIs), Litestar (alt), Django (full-stack).

## Folder layout

```
src/
  <pkg>/
    domain/
    application/
    infra/
      http/
      db/
      clock.py
      random.py
tests/
  unit/
  contract/
  integration/
  e2e/
  fixtures/
```

## Testing patterns

### Domain

Pure `pytest`. Assertions on return values and state changes.

### Hypothesis

```python
from hypothesis import given, strategies as st
from myapp.domain.umbrella import should_take_umbrella

@given(st.lists(st.integers(min_value=0, max_value=100)))
def test_never_errors(probs):
    result = should_take_umbrella(probs, threshold=30)
    assert isinstance(result.needs_umbrella, bool)
```

### FastAPI integration

Use `httpx.AsyncClient` with `ASGITransport(app=app)` — no network, hermetic.

### Database

- Unit: no DB. Repositories mocked/faked.
- Integration: `testcontainers-python` with real Postgres/MySQL when engine-dependent.
- Transactions per test; rollback teardown.

### Contract tests

Share a Pydantic model between production code and contract tests. The contract file in `contracts/` is the Pydantic `schema_json()` export, committed.

## Boundaries

- HTTP: `httpx`; mock with `respx` or with FastAPI's `app.dependency_overrides`.
- DB: SQLAlchemy or SQLModel; test fixtures bind to a throwaway engine.
- Clock: a `Clock` Protocol; inject into services.
- Randomness: inject `random.Random`; seed in tests.

## Linting and types

- `ruff check` and `ruff format --check` in CI.
- `mypy --strict src/`.
- No `Any` in exported public API; use `TypeVar`/`Protocol`.

## Plan decisions (typical)

- Async vs. sync: FastAPI async by default; sync Django if compatibility matters.
- Packaging: single package per service; monorepo via `uv workspaces` when multiple.
- Config via `pydantic-settings`.
- Logging via `structlog`.
