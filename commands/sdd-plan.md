---
description: Generate plan.md, research.md and contracts/ for the active feature (SDD phase 3).
---

Read first:
- @~/Projects/spec-tdd-kit/AGENTS.md
- @~/Projects/spec-tdd-kit/phases/03-plan.md
- @~/Projects/spec-tdd-kit/templates/plan.md
- @~/Projects/spec-tdd-kit/templates/research.md
- @~/Projects/spec-tdd-kit/templates/contracts.md
- @~/Projects/spec-tdd-kit/tools/mcp-context7.md
- @~/Projects/spec-tdd-kit/tools/claude-code-subagents.md

Preconditions:
- `.specs/NNN-*/spec.md` exists with zero `[NEEDS CLARIFICATION]`.
- `.specs/constitution.md` exists.

Goal: produce `plan.md`, `research.md`, and `contracts/*` for the active feature. No code yet.

Steps:

1. Identify the active feature folder. If unclear, ask.

2. Ask the user in Spanish for stack preferences if not implied by the project:
   - Language and version.
   - Framework.
   - DB / persistence.
   - Third parties.
   - Deployment target.
   Offer defaults from the matching file in `~/Projects/spec-tdd-kit/stacks/`.

3. Research:
   - For every library or service that will be used, call context7:
     - `mcp__plugin_context7_context7__resolve-library-id` then `query-docs`.
   - Fall back to `WebFetch` only if context7 does not index the source.
   - Write one entry per library in `research.md` using the template. Each entry carries `captured: <YYYY-MM-DD>`.

4. For larger features, spawn the `Plan` subagent with the spec, constitution and research as inputs. Ask it for architecture options with tradeoffs. Return to this thread with the subagent's report.

5. Draft `plan.md`:
   - Stack decisions with rationale linking to research or constitution.
   - Module layout using the stack file's convention.
   - Data model with invariants.
   - Boundaries, each mapped to a contract file.
   - Error model.
   - Observability.
   - Security.
   - Test strategy (layers, runners, hermetic envs).
   - Rollout (flags, migration order, rollback) if applicable.

6. Write contracts in `contracts/` per the template:
   - HTTP: `openapi.yaml` or minimal `http.yaml`.
   - Queue / events: JSON schema.
   - Internal boundary: typed interface file.
   Each with `version` and `captured`.

7. Write ADRs in `.specs/adr/NNNN-*.md` for:
   - Decisions with multi-task impact.
   - New dependencies / runtimes / services.
   - Constitution amendments.

8. Present the plan to the user in Spanish, calling out tradeoffs and any constitution conflict.

9. On approval, commit: `plan: NNN initial plan + research + contracts`.

Reminders:
- No writing implementation code during plan.
- No library usage decision without a research entry.
- Every external boundary has at least one contract.
- Research entries older than 60 days are re-queried before proceeding.
