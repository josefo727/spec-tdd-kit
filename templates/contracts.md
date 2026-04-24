# Contracts — Index

> Copy to `.specs/NNN-<kebab-name>/contracts/README.md`. Individual contract files live beside it.

## Purpose

Machine-readable shape of every boundary this feature touches. Feeds contract tests.

## Conventions

- **HTTP**: prefer `openapi.yaml`. If full OpenAPI is overkill, a minimal `http.yaml` with `endpoint/method/params/responses` is acceptable.
- **Queue / event**: JSON Schema (`*.schema.json`).
- **Internal boundary**: a typed interface file in the stack's language (`settings.ts`, `Clock.php`, `clock.py`, `Clock.rs`, etc.).
- **Filesystem**: a short `fs.md` with path patterns, file formats, retention.

## Required fields per contract

Every contract file begins with:

```
# contract: <name>
# version: <semver>
# captured: <YYYY-MM-DD>
# source: <research.md entry or internal origin>
```

Then the body.

## Example (http.yaml)

```yaml
# contract: weather-open-meteo
# version: 1.0.0
# captured: 2026-04-24
# source: research.md §open-meteo

endpoint: https://api.open-meteo.com/v1/forecast
method: GET
params:
  latitude:  { type: number, required: true }
  longitude: { type: number, required: true }
  hourly:    { type: string, required: true, const: precipitation_probability }
  forecast_hours: { type: integer, required: false, default: 12 }
  timezone:  { type: string, required: false, default: auto }

responses:
  "200":
    schema:
      hourly:
        time: [string]
        precipitation_probability: [integer]
  "4xx":
    schema:
      reason: string
```

## Example (JSON Schema for queue message)

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "order-created.v1",
  "type": "object",
  "required": ["id", "total", "currency", "createdAt"],
  "properties": {
    "id":        { "type": "string", "format": "uuid" },
    "total":     { "type": "number", "minimum": 0 },
    "currency":  { "type": "string", "pattern": "^[A-Z]{3}$" },
    "createdAt": { "type": "string", "format": "date-time" }
  },
  "additionalProperties": false
}
```

## Versioning

- Bump `version` on any breaking change.
- Keep previous versions available until consumers migrate; mark deprecated in comments.
- Consumer and provider each pin a version; mismatches are caught by contract tests.

## Consumer vs. provider

- **Consumer contract test**: our code stubs the boundary and asserts we send/accept per contract.
- **Provider verification**: if we own the other side, a complementary test asserts the provider returns per contract. If a third party owns the provider, run a periodic contract check job (nightly, weekly).
