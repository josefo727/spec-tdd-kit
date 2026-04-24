# Research — NNN-<kebab-name>

> Copy to `.specs/NNN-<kebab-name>/research.md`. One entry per external library, service, or specification consulted.

## `<library-or-service>@<version>`

- **captured:** `<YYYY-MM-DD>`
- **source:** `<context7 | WebFetch | docs URL>`
- **why consulted:** `<short reason>`

### Relevant API shape

```
<method / endpoint / function signature / event schema>
```

### Gotchas, rate limits, versioning

- `<point>`
- `<point>`

### Decision impact

- Ties to `plan.md` §`<section>`: `<how this finding shaped the decision>`

---

## `<next-library>@<version>`

- **captured:** `<…>`
- **source:** `<…>`
- …

---

## Notes

- Keep entries dated. `captured` is checked during `verify` for freshness (>60 days → re-query).
- If a source was WebFetch, include the URL even when summarized.
- If findings changed a spec or constitution assumption, record an ADR.
