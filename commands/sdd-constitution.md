---
description: Draft or amend the project constitution (SDD phase 0).
argument-hint: "[optional: focus, e.g., 'add article on observability']"
---

Read these files before acting:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/00-constitution.md
- @~/Projects/spec-tdd-kit/templates/constitution.md

Goal: produce or amend `.specs/constitution.md` at the project root.

Steps:

1. Detect whether `.specs/constitution.md` exists.
   - If not, bootstrap it from the template.
   - If yes, treat this run as an amendment.

2. Ask the user in Spanish:
   - What is the primary purpose of this project? (one sentence)
   - Is this a greenfield or brownfield project?
   - For brownfield: do they want me to extract `Current (de facto)` articles by scanning the code? If yes, use the `Explore` subagent and inspect.

3. Start from the mandatory article set in `phases/00-constitution.md`:
   - Article I  — Spec-anchored
   - Article II — Test-first
   - Article III — Boundary-only mocks
   - Article IV — Contract-first integration
   - Article V  — No silent clarification
   - Article VI — ADR for non-local decisions
   Fill them in; rationale and enforcement mandatory.

4. Ask the user which optional articles from the library apply (present them as a multi-choice list via the AskUserQuestion tool if available; otherwise an enumerated list in-chat):
   - Type safety
   - Coverage floor
   - Dependency hygiene
   - Observability
   - Accessibility
   - Security defaults
   - Migration reversibility
   - Time injection
   - Randomness injection
   - Feature-flag expiration

5. Declare the Article VII — Boundaries. Ask for the concrete boundary entries that apply to this project. Do not invent.

6. If amending:
   - Diff the new document against the current one.
   - Open an ADR at `.specs/adr/NNNN-amend-article-<id>.md` via the ADR template.
   - Update the `Amendments` table.

7. Present the document to the user in Spanish, article by article, asking for explicit confirmation.

8. On approval, write the file, commit with message:
   - First time:      `spec: initial constitution v1`
   - Amendment:       `spec: amend article N (see ADR NNNN)`

Reminders:
- No silent downgrades.
- Articles must be enforceable (test, lint, review gate).
- For brownfield, use two-section structure: `Current` and `Aspirational`.
- Never skip the Boundaries article.
