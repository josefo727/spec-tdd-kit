---
description: Draft a feature spec (SDD phase 1). Stack-free, acceptance-criteria-first.
argument-hint: "<feature name in kebab-case or short description>"
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/01-specify.md
- @~/Projects/spec-tdd-kit/templates/spec.md

Goal: produce `.specs/NNN-<kebab-name>/spec.md` for a new feature. No stack choices. Acceptance criteria are observable.

Steps:

1. Verify `.specs/constitution.md` exists. If not, stop and ask the user to run `/sdd-constitution` first.

2. Derive the feature slug from the user's argument:
   - Kebab-case, short, unambiguous.
   - Next free three-digit prefix looking at `.specs/NNN-*` directories.
   - Propose the resulting folder name to the user; wait for OK.

3. Create the folder and `contracts/` subfolder.

4. Ask the user in Spanish, in order:
   - Role of the primary user.
   - Capability the feature grants.
   - Outcome that capability produces.
   - Three to seven acceptance criteria, observable.
   - Anything explicitly out of scope (non-goals).
   Use AskUserQuestion for clarity when two paths are plausible.

5. Draft the spec from the template. Insert `[NEEDS CLARIFICATION: …]` markers anywhere you would otherwise invent a detail. Do not guess.

6. Identify which constitution articles apply; list them in the "Applicable constitution articles" section.

7. Present the draft in Spanish, highlighting:
   - Number of acceptance criteria.
   - Each marker with its resolution question.
   - Non-goals.

8. Ask the user to approve or tweak. On approval, commit:
   `spec: NNN initial spec with K clarifications pending`

Reminders:
- No stack choice in the spec.
- No implementation steps.
- No algorithm names unless they are observable behavior.
- Prefer one criterion per line. Atomic.
- Wording declarative, present tense.
- Keep the spec fitting on one or two screens; slice if longer.
