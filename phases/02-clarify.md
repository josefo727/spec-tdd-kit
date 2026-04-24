# Phase 02 — Clarify

## Purpose

Resolve every `[NEEDS CLARIFICATION: …]` marker in the current spec, explicitly, with the user. No silent resolution.

## Artifact

No new file by default. The spec is edited in place; resolutions are recorded inline, and a short `.specs/NNN-*/clarify.md` log is appended when decisions are substantive.

## Inputs

- `spec.md` with `[NEEDS CLARIFICATION]` markers.
- User availability for dialog.

## Outputs

- `spec.md` with zero unresolved markers.
- Optional `clarify.md`:
  ```
  # Clarify log — NNN-feature

  ## Q1 — max items per page
  - Options: 10 / 20 / configurable
  - Decision: 20
  - Reason: matches existing pagination elsewhere; see §2.3 of constitution.
  - Date: 2026-04-24
  ```

## Agent procedure

For each marker, in order:

1. **Frame the question.** Summarize the ambiguity in one sentence.
2. **Propose options.** Usually 2–4. Always include:
   - the default/easy path,
   - a more rigorous path,
   - `Other: <free text>`.
3. **Ask via `AskUserQuestion`.** Multi-choice with a free-text fallback. This tool is preferred over narrative prompting because it captures a clean machine-readable answer.
4. **Record.** Edit the spec to remove the marker and reflect the chosen option. If the decision is substantive, append an entry to `clarify.md`.
5. **Never move on with unresolved markers.**

### Example

Before:
```
4. The list shows at most [NEEDS CLARIFICATION: 10 or 20?] items per page.
```

Question (to user):

> ¿Cuántos ítems como máximo por página?
> 1. 10 (estándar minimalista)
> 2. 20 (coincide con otras listas del proyecto)
> 3. Configurable por el usuario en ajustes
> 4. Otro (texto libre)

User picks `2`. After:
```
4. The list shows at most 20 items per page.
```

`clarify.md` gets a Q&A block linking to §2.3 of the constitution if relevant.

## When the user says "tú decides"

Allowed, but with constraints:

- The agent picks the **default/easy path**.
- The agent records the Q, the options shown, and the auto-pick, including the label "auto-picked with user delegation".
- The user retains the right to overturn later; an overturn is a spec amendment, not a clarify re-run.

## When a clarification reveals a scope issue

Sometimes the dialog uncovers that the spec is wrong, not merely ambiguous. Stop the clarify loop. Go back to `specify` for the affected sections. Note in the commit: `spec: NNN scope corrected during clarify`.

## Quality checks

- Zero `[NEEDS CLARIFICATION]` markers in `spec.md`.
- Every decision is traceable to a question, either inline (trivial) or in `clarify.md` (substantive).
- No decision contradicts the constitution.

## Slash command

`/sdd-clarify`. See [`../commands/sdd-clarify.md`](../commands/sdd-clarify.md).

## Cross-references

- Previous phase: [`./01-specify.md`](./01-specify.md)
- Next phase: [`./03-plan.md`](./03-plan.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`[NEEDS CLARIFICATION] auto-resolved`)
