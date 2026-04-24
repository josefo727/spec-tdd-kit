# Checklist — Spec

Use before committing a `spec.md` at the end of `/sdd-specify` or after `/sdd-clarify`.

## Scope

- [ ] Title and summary present.
- [ ] Primary user story in the `As a … I want … so that …` shape.
- [ ] 3–7 acceptance criteria. More than 7 is a sign of a feature too large.
- [ ] Non-goals section non-empty.
- [ ] Applicable constitution articles listed.

## Criteria quality

- [ ] Each criterion is observable (behavior, output, side effect).
- [ ] Each criterion is atomic. Split compound criteria joined by "and".
- [ ] No criterion implies a specific stack or library.
- [ ] No criterion uses weasel words ("fast", "robust", "user-friendly").
- [ ] Performance or a11y budgets, when stakeholder-visible, are specified with numbers/standards.

## Ambiguity

- [ ] After `/sdd-clarify`: zero `[NEEDS CLARIFICATION]` markers.
- [ ] If a marker remains intentionally (exploratory spike), it is labeled "NON-BLOCKING" and the next phase is deferred.

## Style

- [ ] Wording declarative, present tense.
- [ ] No references to implementation files, classes, or internal data structures.
- [ ] Fits on one or two screens.

## Approval

- [ ] User reviewed and approved in Spanish.
- [ ] Commit: `spec: NNN initial spec with K clarifications pending` or `spec: NNN clarifications resolved`.
