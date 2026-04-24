---
description: Onboard the kit onto an existing project (brownfield intake).
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/workflows/brownfield.md
- @~/Projects/spec-tdd-kit/phases/00-constitution.md
- @~/Projects/spec-tdd-kit/templates/constitution.md

Goal: produce `.specs/constitution.md`, `.specs/onboarding.md`, `.specs/test-inventory.md`, and `.specs/index.md` for an existing repo — without touching existing code.

Steps:

1. Intake. Ask the user in Spanish and record answers in `.specs/onboarding.md` under `## Intake`:
   - Primary language and framework, versions.
   - Package manager, test runner, test command.
   - Where tests live.
   - CI provider, commands.
   - Deployment target.
   - Size (LOC, module count).
   - Current pain points.

2. Discovery. Use the `Explore` subagent with this brief:
   > Map the repo. Report: top-level structure, languages, frameworks, entry points, external services, DBs, queues, auth. One page.
   Write the report under `.specs/onboarding.md` `## Discovery`.

3. Extract the `Current (de facto)` constitution:
   - Inspect code for recurring patterns that qualify as de facto rules.
   - Draft each rule with an example file path as evidence.
   - Examples:
     - "Tests written in Pest functional syntax under tests/Feature/."
     - "All HTTP out goes through App\Services\Http\HttpClient."
   - Do not invent rules.

4. Ask the user which current articles to keep (they all are de facto; this is a curation step, not a gate). For each, ask whether it is `Current` (to keep) or `Current-to-replace` (to replace via an Aspirational article).

5. Draft Aspirational articles for gaps, especially the mandatory set from `phases/00-constitution.md`:
   - Spec-anchored, Test-first, Boundary-only mocks, Contract-first, No silent clarification, ADR for non-local decisions.
   - Each aspirational article includes `applies_from: <commit|date>` and `migration: <touch-fix | scheduled | ADR-driven>`.

6. Inventory tests. Write `.specs/test-inventory.md`:
   - Coverage (run the tool; record the number).
   - Pyramid shape (counts by layer, estimated).
   - Slow tests (>500ms).
   - Flaky tests if history available.
   - Test smells.

7. Build the brownfield index `.specs/index.md`:
   - Modules currently under legacy rules.
   - Boundaries (HTTP, DB, queue, clock, randomness, third parties).
   - Empty section: `Modules under SDD+TDD` (populated as features close).

8. Add or update the project's `CONTRIBUTING.md` (if the user agrees) with a line:
   > New features follow the spec-tdd-kit workflow: `@~/Projects/spec-tdd-kit/workflows/feature-flow.md`.

9. Commit as one logical commit:
   `spec: brownfield onboarding (constitution v1, inventory, index)`

10. Recommend next action: run `/sdd-specify` for the first kit-managed feature.

Reminders:
- Brownfield constitution must describe reality first; aspiration second.
- No code changes during onboarding.
- Keep the scope bounded: the onboarding is a starting point, not a rewrite.
