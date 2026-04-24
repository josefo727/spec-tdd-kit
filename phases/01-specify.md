# Phase 01 — Specify

## Purpose

Describe **what** a feature does and **why**, in observable terms. The spec is the contract between stakeholder intent and implementation.

## Artifact

`.specs/NNN-<kebab-name>/spec.md`. Populated from [`../templates/spec.md`](../templates/spec.md).

## Inputs

- Constitution (applied constraints).
- User intent (prompt or conversation).
- Domain context (prior features, glossary).

## Outputs

A `spec.md` containing:

- **Title and summary.**
- **User story (or stories).** `As <role>, I want <capability>, so that <outcome>.`
- **Acceptance criteria.** Numbered, in observable terms. Prefer one line each.
- **Non-goals.** Things explicitly excluded from this feature's scope.
- **Applicable constitution articles.** By number.
- **Open questions**: any `[NEEDS CLARIFICATION: …]` markers.
- **Glossary additions** (optional).

## Content rules

### What belongs in a spec

- Observable behavior: inputs, outputs, state changes, side effects the user cares about.
- User-facing error conditions.
- Performance budgets, when stakeholder-visible ("search returns in under 500 ms at p95").
- Accessibility or localization requirements, when stakeholder-visible.

### What does **not** belong in a spec

- Stack choice (React vs. Vue; Pest vs. PHPUnit). That lives in `plan.md`.
- File paths, class names. Internal concerns.
- Algorithm names or data structures, unless they are externally observable.
- Marketing copy.

### Wording style

- Declarative, present tense: "the system returns", not "the system should return".
- Avoid weasel words: "fast", "robust", "user-friendly" are forbidden. Replace with measurable terms.
- One criterion per line.

### Examples

Good:
```
3. The system accepts a city name of 1–80 UTF-8 characters.
4. If the city does not exist in the geocoding index, the system responds with a 404 and body `{ "error": "unknown_city" }`.
5. The response includes the forecast for the next 12 hourly slots, in the user's locale timezone.
```

Bad (hidden plan, hidden ambiguities):
```
3. The system handles city names robustly using our CityValidator class.
4. Errors are returned gracefully.
5. Forecasts are displayed nicely.
```

### `[NEEDS CLARIFICATION]` discipline

Whenever the agent would need to *invent* a decision to proceed, it emits a marker instead:

```
4. The list shows at most [NEEDS CLARIFICATION: 10 or 20?] items per page.
```

Marker format: `[NEEDS CLARIFICATION: <concise question or options>]`. Markers are resolved in the clarify phase. Never silently.

## Agent procedure

1. Read `constitution.md`. Cache applicable articles.
2. Parse the user prompt. Identify role, capability, outcome.
3. Draft the spec using [`../templates/spec.md`](../templates/spec.md).
4. For every decision the agent would have to invent, emit `[NEEDS CLARIFICATION]`.
5. Present the spec to the user in Spanish, point out the markers, ask for adjustments before moving on.
6. Commit `spec: NNN initial spec with K clarifications pending`.

## Quality checks (apply before moving on)

- Every acceptance criterion is observable.
- No criterion implies a specific stack.
- Each criterion is atomic (if you can split with "and", split).
- The spec fits on one screen or two. If longer, consider slicing the feature.
- The non-goals section exists and is non-empty.
- The spec references the constitution articles it honors.

## Slash command

`/sdd-specify` executes this phase. See [`../commands/sdd-specify.md`](../commands/sdd-specify.md).

## Cross-references

- Template: [`../templates/spec.md`](../templates/spec.md)
- Checklist: [`../checklists/spec.md`](../checklists/spec.md)
- Clarify phase: [`./02-clarify.md`](./02-clarify.md)
