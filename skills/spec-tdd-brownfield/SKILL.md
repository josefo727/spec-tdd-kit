---
name: spec-tdd-brownfield
description: Adopt Spec-Driven Development and Test-Driven Development in an existing codebase. Extracts a constitution from current code, inventories tests, builds a brownfield index, then drives the first kit-managed feature. Use when the user wants to add SDD+TDD to an existing repo without rewriting it.
---

# spec-tdd-brownfield

You are helping the user onboard the spec-tdd-kit onto an **existing** repository.

## Preconditions

- Repo has real code.
- User understands the kit will apply to new/touched surfaces, not rewrite past code.
- Kit is present at `~/Projects/spec-tdd-kit/`.

## Reading list

1. `~/Projects/spec-tdd-kit/AGENTS.md`
2. `~/Projects/spec-tdd-kit/workflows/brownfield.md`
3. `~/Projects/spec-tdd-kit/phases/00-constitution.md`
4. `~/Projects/spec-tdd-kit/anti-patterns.md` (focus on "Brownfield constitution invented, not extracted")

## Language protocol

- User: Spanish.
- Artifacts: English.

## Steps

1. Run `/sdd-onboard` which:
   - Captures Intake answers.
   - Delegates repo discovery to the `Explore` subagent.
   - Extracts `Current (de facto)` articles with evidence.
   - Drafts `Aspirational` articles for gaps.
   - Writes `.specs/constitution.md`, `.specs/onboarding.md`, `.specs/test-inventory.md`, `.specs/index.md`.
   - Commits.

2. Recommend a first feature (small, vertical slice on a module where tests already exist). Drive it with:
   - `/sdd-specify`, `/sdd-clarify`, `/sdd-plan`, `/sdd-tasks`, `/sdd-implement`, `/sdd-verify`.

3. After the first feature closes:
   - Update `.specs/index.md` to list the module now under SDD+TDD.
   - Retro: surface one friction ("test runner slow", "contract fixture missing") and propose an improvement.

## Tools you will use

- `Agent` with `Explore` subagent for discovery. Keep the report under one page.
- `Bash` for `git log`, `find`, coverage runners.
- `Read` for code inspection.
- `AskUserQuestion` for intake and constitution curation.

## Boundaries

- Do not refactor legacy code during onboarding. Tempting but off-scope.
- Do not invent principles the code does not follow — extract first.
- Do not migrate the whole test suite to a new runner during onboarding. Touch-fix as features edit files.

## When to stop and ask

- The repo has no tests at all: confirm with the user whether the first feature will add a minimal harness before writing the app feature.
- The user resists a mandatory article (test-first): present the conflict and offer paths:
  (a) adopt the article only for new code via Aspirational,
  (b) adopt a weaker alternative with a documented risk,
  (c) keep improvising (not using the kit).
