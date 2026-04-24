---
name: spec-tdd-greenfield
description: Bootstrap a new project with Spec-Driven Development and Test-Driven Development. Drives constitution, specify, clarify, plan, tasks, implement, verify. Use when the user starts a new repo or new service from scratch and mentions SDD, TDD, spec-driven, or both.
---

# spec-tdd-greenfield

You are helping the user bootstrap a **new** project using the spec-tdd-kit.

## Preconditions

- Repository is empty or nearly empty.
- User intends to adopt SDD+TDD.
- Kit is present at `~/Projects/spec-tdd-kit/`.

## Reading list (read these in order before acting)

1. `~/Projects/spec-tdd-kit/AGENTS.md`
2. `~/Projects/spec-tdd-kit/workflows/greenfield.md`
3. `~/Projects/spec-tdd-kit/philosophy/03-sdd-tdd-fusion.md`

## Language protocol

- Talk to the user in Spanish.
- Write all artifacts in English.

## Steps

1. Confirm in Spanish that the user wants the greenfield workflow (not brownfield).

2. Run the bootstrap: `git init` if needed, create `.specs/adr/`, propose a minimal `.gitignore`.

3. Drive the phases in order using slash commands where available:
   - `/sdd-constitution` → `.specs/constitution.md`
   - `/sdd-specify`      → `.specs/NNN-<name>/spec.md`
   - `/sdd-clarify`      → resolve markers
   - `/sdd-plan`         → plan, research, contracts
   - `/sdd-tasks`        → tasks.md with R-G-F
   - `/sdd-implement`    → TDD loop (`/tdd-red`, `/tdd-green`, `/tdd-refactor`)
   - `/sdd-verify`       → R1–R10 checks

4. Between phases:
   - Require user approval.
   - Commit the artifact with the message conventions listed in the phase docs.

5. If the feature projects larger than a few days, slice it and do one vertical slice first.

6. Close the feature per `phases/06-verify.md` and propose the next feature.

## Tools you will use

- `Read`, `Write`, `Edit`, `Bash` for artifacts and git.
- `AskUserQuestion` during `clarify`.
- `mcp__plugin_context7_context7__*` during `plan`.
- `Agent` with `Plan`, `Explore`, or stack-specific subagents as needed.

## Boundaries

- Do not write production code before the Red commit for that task.
- Do not fill `[NEEDS CLARIFICATION]` silently.
- Do not amend the constitution without an ADR.

## When to stop and ask

- Constitution article conflicts with a spec decision.
- Plan introduces a boundary the constitution does not declare.
- Red test cannot be made to fail for the right reason after 3 attempts.
- Verify reports a FAIL you cannot fix locally.
