# Anti-patterns

Things that look like SDD+TDD but are not. Call them out early; they silently erode both practices.

## 1. Spec-once development

**Symptom.** A spec is written on day one and never updated. Commits diverge from spec wording within a sprint.

**Why it happens.** Writing the initial spec feels like work completed; maintaining it feels like overhead.

**Defense.**
- Treat `spec.md` as a deliverable of every feature commit.
- In the verify phase, fail when code changes but no spec section changed for the same feature.
- Use the `/sdd-verify` slash command.

## 2. Vibe coding behind an SDD costume

**Symptom.** The spec is three bullet points. The plan is copied from a tutorial. Tasks are generated but never read. Someone went straight to implement.

**Defense.**
- Gate: no `implement` before `plan.md` and `tasks.md` exist and are user-approved.
- If the feature is genuinely tiny, use the **feature-flow lite** path in [`workflows/feature-flow.md`](./workflows/feature-flow.md) — do not hide it under the full flow.

## 3. Tautology tests

**Symptom.** Tests mirror the implementation line by line. They pass but cannot catch mistakes of reasoning. Refactoring requires rewriting the tests.

**Defense.**
- Write tests from the **spec acceptance criteria**, not from the finished code.
- Prefer example-based scenarios ("given input X, output Y"), not "the function calls A, then B, then C".
- Add a small property-based test per pure function where the language supports it.

## 4. Test-after disguised as test-first

**Symptom.** Developer writes code, then writes a test that "would have failed", commits both together. No Red commit exists.

**Defense.**
- Enforce visible Red: commit the failing test first (`test: T042 red — …`), verify CI/local runs show the failure, then commit Green.
- The kit's `/tdd-red` slash command captures the failing output and attaches it to the commit body.

## 5. Mock creep

**Symptom.** Every collaborator is mocked. Tests pass in isolation and the integration is broken.

**Defense.**
- Constitution article: "Mocks only at declared boundaries (HTTP, clock, randomness, filesystem, process)." See [`templates/constitution.md`](./templates/constitution.md).
- Domain logic uses real collaborators unless cost is prohibitive.
- Add one integration test per boundary per feature that exercises the real boundary in a hermetic env.

## 6. `[NEEDS CLARIFICATION]` auto-resolved

**Symptom.** The agent notices ambiguity, writes `[NEEDS CLARIFICATION]`, then fills it in without asking the user.

**Defense.**
- Hard rule in [`AGENTS.md`](./AGENTS.md): `[NEEDS CLARIFICATION]` can only be removed after explicit user input captured verbatim in `clarify.md`.
- Use the `AskUserQuestion` tool, not guesswork.

## 7. Waterfall theatre

**Symptom.** Three weeks spent on spec + plan + research before a single test runs. The first green test reveals the plan is wrong.

**Defense.**
- Target cycle time: constitution-to-first-green in one session for small features, one day for medium features.
- For larger efforts, slice into sub-features (`NNN-<slice>`) and run them in sequence.
- If the first green test reveals a wrong assumption, amend the spec — that is the loop working, not failing.

## 8. Constitution creep

**Symptom.** New principles added every sprint. The document becomes a pile of preferences.

**Defense.**
- Principles must be declarable as tests or as review criteria. Preferences go in stack appendices or team docs.
- Amendments require an ADR.

## 9. Contracts-as-afterthought

**Symptom.** `contracts/` is empty or written after implementation. Integration bugs appear at the boundary.

**Defense.**
- Gate: plan is not complete without at least one contract per external boundary.
- Contract tests are scheduled in `tasks.md` **before** the feature tests that depend on them.

## 10. Parallel-`[P]` abuse

**Symptom.** Most tasks are marked `[P]`. The agent runs them concurrently, one writes the same file as another, merges break.

**Defense.**
- `[P]` is allowed only when tasks touch disjoint files AND share no mutable state.
- During `tasks` phase, verify file coverage with a sanity pass: list files touched per task; any overlap removes `[P]`.

## 11. "The agent one-shotted it, we're done"

**Symptom.** Generated code compiles, tests pass, nobody reads the diff. Security debt accumulates.

**Defense.**
- Non-negotiable human review for code that touches auth, money, PII, or external writes.
- Run a `security-review` pass at `verify` for those surfaces.

## 12. Stale research

**Symptom.** `research.md` was written months ago. The library's API changed. Tests assume the old shape.

**Defense.**
- Research entries carry a `captured: YYYY-MM-DD` marker.
- Before a feature enters `implement`, re-query `context7` for any API older than 60 days.

## 13. Refactor phase skipped systematically

**Symptom.** Green commits accumulate. Tech debt accumulates. Nobody ever refactors.

**Defense.**
- Per task, if refactor was skipped, record the reason in the task entry (`F: skipped — no smell detected`). Audit skipped-refactor counts weekly; a high count is a smell.

## 14. Brownfield constitution invented, not extracted

**Symptom.** Adopting the kit on an existing repo, the agent writes a constitution of ideals the code does not follow. Every new task fails the constitution.

**Defense.**
- Brownfield constitution is **extracted** first (what the code actually does), then **aspirational** articles go into a separate section marked `aspirational`, with scheduled migrations.
- See [`workflows/brownfield.md`](./workflows/brownfield.md).

## 15. Skipping the verify phase on small features

**Symptom.** Small features bypass `verify`. Inconsistencies between spec, code, and tests pile up across many features.

**Defense.**
- `verify` on small features is cheap (5 minutes with `/sdd-verify`). Run it.
- The kit's checklist in [`checklists/done.md`](./checklists/done.md) is non-negotiable.

## 16. Undated rule pile

**Symptom.** The guide file (`AGENTS.md`, `CLAUDE.md`, constitution) grows one rule at a time with no dates and no trace to the failure that motivated each rule. Months later, rules contradict each other, nobody dares delete anything, and the agent starts ignoring the file.

**Defense.**
- Every rule born from a failure carries a date and a one-line trace. Format and pruning schedule in [`ratchet.md`](./ratchet.md).
- Monthly prune: delete rules now enforced by hooks/tests/permissions, consolidate duplicates, re-justify or delete untraceable rules.
- A guide file that only grows is technical debt, not a harness.

## 17. Trust-based verification

**Symptom.** Checks that only run if the agent remembers to run them. The same correction is made in chat for the fourth time. Lint passes locally "most sessions". A rule exists in the guide file, yet the violation keeps landing in commits.

**Defense.**
- Third repetition of a correction is the promotion signal: convert the rule into a hook, a permission boundary, or a test — see the promotion ladder in [`ratchet.md`](./ratchet.md).
- Encode the project's permission boundary in a committed `.claude/settings.json` ([`templates/claude-settings.md`](./templates/claude-settings.md)) instead of relying on interactive prompts.
- The test: would the check still run if the agent forgot the rule existed? If no, it is trust, not verification.
