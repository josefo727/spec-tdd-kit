# Workflow — Feature flow

The everyday path. A project is already under SDD+TDD (greenfield bootstrapped or brownfield onboarded). You add a feature.

## Preconditions

- `.specs/constitution.md` exists and is current.
- Test runner, linter, CI are wired.
- Claude Code session in project root.
- `context7` MCP available.

## Phase map

Identical to greenfield, minus bootstrap and constitution:

```
1. Specify    2. Clarify    3. Plan    4. Tasks    5. Implement    6. Verify    7. Close
```

## Branch strategy

- Create a branch: `git checkout -b feat/NNN-kebab-name`.
- Number `NNN` = next free number under `.specs/`.
- Feature folder `.specs/NNN-kebab-name/` is born empty and is populated phase by phase.

## Lite variant

For a feature that is **objectively small** (one small function, one button, no new external dependency, no new boundary), skip the separation and use the **feature-flow-lite** path:

- One file: `.specs/NNN-kebab-name/spec-plan-tasks.md` (a fused mini-artifact).
- Three sections: `Spec`, `Plan (one paragraph)`, `Tasks (3–10 R-G-F triples)`.
- Normal TDD in `implement`.
- Normal `/sdd-verify`.

Criteria for lite:
- Zero new dependencies.
- Zero new boundaries.
- Zero data model changes.
- Estimated one working session.

If any criterion fails, use the full flow.

## Full flow

Steps mirror [greenfield](./greenfield.md) from phase 2 onward. Reference:

1. `/sdd-specify` → `spec.md` with acceptance criteria and `[NEEDS CLARIFICATION]` markers.
2. `/sdd-clarify` → resolve markers with `AskUserQuestion`.
3. `/sdd-plan` → `plan.md`, `research.md`, `contracts/`, optional ADR. `context7` is mandatory for any new library usage.
4. `/sdd-tasks` → `tasks.md` with `T{NNN}` entries and R-G-F.
5. `/sdd-implement` opens the TDD loop; per-task: `/tdd-red`, `/tdd-green`, `/tdd-refactor`.
6. `/sdd-verify` → R1–R6 checks.
7. Close:
   - Update `spec.md` with a `## Closed` footer.
   - If brownfield, update `.specs/index.md` to reflect new module coverage.
   - Commit, PR, merge per team policy.

## Gates that apply here but were implicit in greenfield

- **Constitution compatibility.** Spawn no spec or plan that violates an article. If a violation is unavoidable, open an ADR amending the constitution; merge the ADR before the feature.
- **Research freshness.** If `plan.md` cites a library and the last `research.md` entry for that library is older than 60 days, re-run `context7` during `/sdd-plan`.
- **Contract reuse.** If the boundary is covered by an existing contract, extend it; do not duplicate.

## Common pitfalls

- Reusing an old feature's folder for a new feature (don't). New folder, new number.
- Touching code on `main` instead of the feature branch. All feature work on branch.
- Forgetting to update the brownfield index after closing. Automatable via `/sdd-verify`.

## Cross-references

- Greenfield: [`./greenfield.md`](./greenfield.md)
- Brownfield: [`./brownfield.md`](./brownfield.md)
- Phases: [`../phases/`](../phases/)
- Checklists: [`../checklists/`](../checklists/)
