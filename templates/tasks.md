# Tasks — NNN-<kebab-name>

> Copy to `.specs/NNN-<kebab-name>/tasks.md`. Each task is atomic: one R-G-F cycle's worth of work.

## Legend

- `T{NNN}` — task id, unique within feature, zero-padded.
- `[P]` — safe to execute in parallel with other `[P]` tasks (disjoint files, no shared mutable state).
- `R` — Red beat description.
- `G` — Green beat description.
- `F` — Refactor beat description.
- `status` — `open | in_progress | closed | skipped`.

---

## T001 `<Title>`

```
spec-ref:        <§ of spec.md>
contract-ref:    <file or n/a>
constitution-ref:<Article N or n/a>
DoD:
  - <checklist bullet 1>
  - <checklist bullet 2>
R: <failing test description — what assertion will fail>
G: <minimal implementation description — what file/function appears>
F: <refactor hint — extract/rename/deduplicate>
files:
  - <path>
  - <path>
status: open
commits:
  red:
  green:
  refactor:
notes:
```

---

## T002 [P] `<Title>`

```
spec-ref:
contract-ref:
constitution-ref:
DoD:
  - …
R:
G:
F:
files:
  - …
status: open
commits:
  red:
  green:
  refactor:
notes:
```

---

## …

---

## Amendments

| Date | Change | Reason |
|------|--------|--------|
|      |        |        |
