# Stack — Go

## Defaults

- **Version:** Go 1.23+.
- **Module layout:** single module or workspace (`go.work`) for multi-service.
- **Test runner:** built-in `go test`; `gotestsum` for nicer output.
- **Assertions:** `testify/assert` or `testify/require`; `cmp` for deep equality.
- **HTTP client:** standard `net/http`.
- **Web framework:** stdlib `net/http` (preferred for services) or `chi`/`gin` if needed.
- **DB:** `database/sql` + `pgx` (Postgres) or `sqlc` for typed queries.
- **Property-based:** `testing/quick` or `rapid`.

## Folder layout

```
cmd/<bin>/           # entrypoints
internal/
  domain/
  application/
  infra/
    http/
    db/
    clock.go
    random.go
```

Keep `domain/` free of stdlib IO where sensible.

## Testing patterns

### Table-driven tests

Idiomatic Go. Each case is a struct field in a slice; loop asserts.

```go
func TestShouldTakeUmbrella(t *testing.T) {
    cases := []struct {
        name      string
        probs     []int
        threshold int
        want      bool
    }{
        {"below", []int{10, 20}, 30, false},
        {"at",    []int{30, 5},  30, true},
    }
    for _, c := range cases {
        t.Run(c.name, func(t *testing.T) {
            got := ShouldTakeUmbrella(c.probs, c.threshold)
            if got != c.want { t.Fatalf("want %v got %v", c.want, got) }
        })
    }
}
```

### HTTP

`httptest.NewServer` for integration; `httptest.NewRecorder` for handler unit tests.

### DB

- Unit: interfaces + fakes.
- Integration: `testcontainers-go` or disposable docker-compose.

### Property-based

`rapid` preferred over `testing/quick` for expressiveness.

### Contract tests

JSON schema or OpenAPI files in `contracts/`; consumer tests decode responses into generated types.

## Boundaries

- HTTP: `http.Client` injected via constructor.
- DB: interfaces in domain; drivers in infra.
- Clock: `Clock` interface; prod uses `time.Now`; tests use fake.
- Randomness: inject `*rand.Rand`.

## Linting and types

- `go vet ./...`.
- `golangci-lint run` with `errcheck`, `revive`, `staticcheck`, `gosec`.
- `gofmt -d .` in CI.

## Plan decisions (typical)

- Errors: wrap with `fmt.Errorf("…: %w", err)`; sentinel errors for domain-significant ones.
- Context: propagate `context.Context` as first param; never `context.Background()` in libraries.
- Logging: `log/slog` (stdlib) with JSON handler in prod.
- Config: env via `envconfig` or stdlib `flag`.

## Performance

- `go test -bench=.` with `benchstat` for comparisons.
- `pprof` on critical paths.
