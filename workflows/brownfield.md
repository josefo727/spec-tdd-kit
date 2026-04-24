# Workflow — Brownfield

Adopting SDD + TDD in an **existing codebase**. The trick: do not try to retro-spec everything at once. Extract the constitution from what the code *already does*, then apply the full flow only to *new* or *touched* surfaces.

## Preconditions

- Existing repository with real code, probably without `.specs/`.
- You have read access to the test suite (if any) and to deployment config.
- Claude Code session in the project root.
- `context7` MCP available.
- Decision made: you will use the kit for the next feature, not rewrite the past.

## Phase map

```
0. Intake                      5. Build the "brownfield index"
1. Discover                    6. Onboard the kit's plumbing
2. Extract constitution        7. First feature under SDD+TDD
3. Inventory tests             8. Close and schedule migrations
4. Define aspiration
```

---

## 0. Intake

Ask, in Spanish, and record the answers in `.specs/onboarding.md`:

- Primary language and framework, versions.
- Package manager, test runner, test command.
- Where tests live (`tests/`, `__tests__/`, next to source, etc.).
- CI provider and commands.
- Deployment target (matters for integration tests).
- Size of the codebase (LOC, number of modules).
- Current pain points — what made the user adopt SDD+TDD now?

---

## 1. Discover — read without writing

Spawn the `Explore` subagent with a task like:

> Map the repo: top-level structure, languages, frameworks, entry points, external services, database engines, queue systems, auth. Return a one-page report.

Then run these queries manually (or delegate):

```bash
git log --format='%ai %an %s' | head -50
# tests discovery
find . -type d \( -name 'tests' -o -name '__tests__' -o -name 'spec' \) -not -path './node_modules/*' -not -path './vendor/*'
# dependency manifest
ls package.json composer.json requirements.txt pyproject.toml Cargo.toml go.mod pubspec.yaml 2>/dev/null
```

Save a terse summary in `.specs/onboarding.md` under `## Discovery`.

---

## 2. Extract constitution (not invent)

This is the hardest step. The constitution must describe the project **as it is**, not as you wish it were. Later, aspirational articles get their own section.

Run `/sdd-onboard`. The command leads Claude Code through a discovery-to-constitution pass:

1. Inspect code to infer *de facto* rules:
   - "We use Laravel's service container; there are no custom DI wrappers." → Article.
   - "All HTTP calls go through `HttpClient::send()`; tests stub at that point." → Article.
   - "Tests use Pest classic syntax." → Article.
2. Draft **Current Articles** listing each inferred rule with evidence (file paths).
3. Ask the user which current articles to keep, and which to mark for replacement.
4. Propose **Aspirational Articles** for gaps (e.g., "test-first going forward", "boundary-only mocks"). Each aspirational article has a migration note: "applies to new code from commit X; existing violations tracked in `.specs/adr/NNNN-migrate-*.md`".

Output: `.specs/constitution.md` with two sections, `Current` and `Aspirational`.

Gate: user approves. Commit: `spec: brownfield constitution extracted (vX of repo)`.

Detail: [`../phases/00-constitution.md`](../phases/00-constitution.md).

---

## 3. Inventory tests

Characterize the existing suite:

- Coverage (run the tool; record the number).
- Pyramid shape (count tests per layer).
- Slow tests (`>500ms`) and flaky tests (retry history if tracked).
- Test smells (giant fixtures, chained mocks, sleep-based synchronization).

Record in `.specs/test-inventory.md`. Do **not** refactor tests now.

Output a short ranked list of test-debt items to address opportunistically.

---

## 4. Define aspiration

In `.specs/constitution.md` `Aspirational` section, for each gap:

- What the new rule says.
- Starting commit / date.
- How to migrate existing offenders (never "rewrite everything in sprint X" — prefer "touch-fix": when a file is edited for any reason, bring its tests up to the new rule).

---

## 5. Build the "brownfield index"

Create `.specs/index.md`:

```
# Brownfield index

## Modules under SDD+TDD
- (empty; first feature pending)

## Modules under legacy rules
- src/legacy/* — see Aspirational Article 3 migration plan

## Boundaries
- HTTP: HttpClient::send()
- DB: Eloquent, via BaseRepository
- Queue: Laravel queues (redis)
- Time: CarbonImmutable::now()
- Randomness: random_int in Util::secureRandom
```

The index tells Claude Code where it is allowed to mock and what is considered domain.

---

## 6. Onboard the kit's plumbing

- Create `.specs/` top level (if not already) with `constitution.md`, `adr/`, `onboarding.md`, `test-inventory.md`, `index.md`.
- Install slash commands and skills if not yet ([`../INSTALL.md`](../INSTALL.md)).
- Add a `CONTRIBUTING.md` snippet (or update existing) noting: "New features follow `@~/Projects/spec-tdd-kit/workflows/feature-flow.md`".
- Commit: `docs: adopt spec-tdd-kit for future features`.

---

## 7. First feature under SDD + TDD

Use [`feature-flow.md`](./feature-flow.md) for the first real feature. Keep it small. Purpose is to validate the kit fits this repo, and to expose hidden frictions (test runner quirks, slow CI, missing fixtures).

After the first feature closes, update the brownfield index to list the module(s) now under SDD+TDD.

---

## 8. Close and schedule migrations

For each aspirational article:

- If it can be enforced automatically (lint rule, type check, coverage threshold), enable it.
- If it requires touch-fix, keep the article in place; wait for organic edits.
- Big migrations (framework upgrades, test runner swaps) get their own ADR and their own SDD+TDD feature folder.

---

## Variant: quick-path for tiny repos

If the repo is small (< 2 kLOC, low complexity), compress the above:

- Skip the inventory and aspiration split; write a unified constitution.
- Use the feature-flow directly on the next task.

---

## Mini-map

| Step | Slash command |
|---|---|
| Onboard | `/sdd-onboard` |
| First feature | `/sdd-specify` … `/sdd-verify` |
| TDD | `/tdd-red`, `/tdd-green`, `/tdd-refactor` |

## Common pitfalls

- **Inventing a constitution.** The kit fails if articles describe a repo that does not exist. Extract first.
- **Big-bang migration.** Do not rewrite history. Touch-fix.
- **Retrofitting specs to old code.** Nothing wrong with documenting key modules, but that is *narrative*, not `spec.md`. Keep `spec.md` for new features.
- **Treating the test inventory as a backlog.** It is a map. Address items only when you are already editing the file.

## Cross-references

- Constitution phase: [`../phases/00-constitution.md`](../phases/00-constitution.md)
- Feature flow: [`./feature-flow.md`](./feature-flow.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`Brownfield constitution invented, not extracted`)
