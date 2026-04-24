# Glossary

Terms used across the kit. When in doubt, this page rules.

## Core concepts

**SDD — Spec-Driven Development.** A practice where a versioned specification is the source of truth. Code and tests are derived from (and validated against) the spec. Three maturity levels: *spec-first*, *spec-anchored*, *spec-as-source*.

**TDD — Test-Driven Development.** A practice where every production change is driven by a **failing** test written first (Red), followed by the minimal code that makes it pass (Green), followed by cleanup without changing behavior (Refactor).

**SDD + TDD fusion.** The kit's contribution: the SDD phases produce an artifact (`tasks.md`) whose every task enforces a Red-Green-Refactor cycle tied to explicit acceptance criteria.

## Phases

**Constitution.** Immutable project principles (one document, articles). Governs all downstream artifacts.

**Specify.** WHAT and WHY, no tech choices. Output: `spec.md`.

**Clarify.** Resolving `[NEEDS CLARIFICATION]` markers via explicit user dialog.

**Plan.** HOW at the technical level. Output: `plan.md`, `research.md`, `contracts/`.

**Tasks.** Decomposed, ordered, test-first units of work with R-G-F sub-steps. Output: `tasks.md`.

**Implement.** Execution of tasks under TDD.

**Verify.** Cross-artifact consistency check, regression sweep, DoD gate.

## Artifacts

**Spec (`spec.md`).** User stories, acceptance criteria, non-goals, constraints. Spec is stack-agnostic.

**Plan (`plan.md`).** Stack choice, architecture, module boundaries, data flow. References `research.md` and `contracts/`.

**Research (`research.md`).** Notes from `context7` and other sources — rate limits, API shape, gotchas. Every claim carries a source.

**Contracts (`contracts/`).** Machine-readable shape of external and internal boundaries (HTTP endpoints, message schemas, function signatures). Feed property-based and contract tests.

**Tasks (`tasks.md`).** Ordered list of `T{NNN}` entries, each with: spec-ref, contract-ref, constitution-ref, R-G-F sub-steps, DoD, `[P]` flag if parallelizable.

**ADR (`adr/NNNN-title.md`).** Architecture Decision Record. Written when a plan decision has multi-task consequences or amends the constitution.

**Test plan (`test-plan.md`).** Optional. Present for projects where testing strategy is non-trivial (many layers, flaky third parties, high latency).

## TDD jargon

**Red.** A test that fails for the **right reason** (the feature does not yet exist or behaves incorrectly). A test failing for compile/import reasons is not Red; fix the scaffolding first.

**Green.** The minimal code that makes the Red test pass. No speculative additions.

**Refactor.** Behavior-preserving restructuring while all tests remain Green. Optional per task; required when structure degrades.

**Tautology trap.** A test written from the same misunderstanding that produced the implementation. It passes and proves nothing. Defenses: write tests from the spec, not from the code; prefer example-based tests anchored to acceptance criteria; add boundary/property tests.

**Classicist vs. Mockist.**
- *Classicist* (Chicago school) — test behavior via real collaborators when practical; isolate at process boundaries.
- *Mockist* (London school) — isolate each unit with doubles.
The kit defaults classicist for domain logic and mockist only at boundaries (HTTP, time, randomness, filesystem) the constitution declares untestable in-process.

**Test pyramid.** Many unit tests, fewer integration tests, few end-to-end tests. The kit does not enforce ratios; it enforces that every acceptance criterion is covered at some level.

**Contract test.** A test that asserts your code honors a contract with an external system. Paired with provider verification on the other side when possible.

## Claude Code jargon

**Skill.** A reusable instruction set at `~/.claude/skills/<name>/SKILL.md` that Claude Code auto-invokes when the description matches user intent.

**Slash command.** A file at `~/.claude/commands/<name>.md` invoked with `/name`. Its body is injected as the prompt.

**Subagent.** A specialized agent spawned via the `Agent` tool (e.g., `Plan`, `Explore`, `laravel-debugger`).

**MCP — Model Context Protocol.** Servers that expose tools to Claude Code. This kit mandates `context7` for library research.

**`@` reference.** Prompt syntax to pass a file's contents into the conversation (`@path/to/file.md`).

## Conventions used in this kit

**`[NEEDS CLARIFICATION: <question>]`.** Inline placeholder inside a spec/plan when a decision is pending.

**`[P]`.** Suffix on a task entry meaning it is safe to execute in parallel with others also marked `[P]`.

**`T{NNN}`.** Task identifier, zero-padded to three digits. Unique within a feature.

**`DoD — Definition of Done`.** Per-task checklist. At minimum: Red test committed, Green test passing, Refactor done or declared unnecessary, lint/format clean, no new warnings.

**Feature folder.** `.specs/NNN-kebab-name/` at project root.

**Generic vs. stack.** "Generic" guidance applies regardless of language. "Stack" guidance overrides generic when present.
