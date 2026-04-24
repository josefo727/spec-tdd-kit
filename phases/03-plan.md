# Phase 03 — Plan

## Purpose

Turn the spec (WHAT/WHY) into a technical plan (HOW). Pick the stack, draw module boundaries, declare contracts, back decisions with research.

## Artifacts

- `.specs/NNN-<kebab-name>/plan.md` — main plan.
- `.specs/NNN-<kebab-name>/research.md` — cited findings.
- `.specs/NNN-<kebab-name>/contracts/*` — machine-readable boundaries.
- `.specs/adr/NNNN-*.md` — ADRs for non-local decisions (zero or more).

## Inputs

- Approved `spec.md`.
- `constitution.md` (articles apply).
- Existing code and its conventions (for brownfield).

## Outputs

### `plan.md`

Populated from [`../templates/plan.md`](../templates/plan.md). Sections:

1. **Summary.** One paragraph mapping the spec to the proposed architecture.
2. **Stack decision.** Language, framework, test runner, linter, type checker, build tool, target runtime. Each item carries a rationale pointing to a research entry or a constitution article.
3. **Module layout.** Directories, entry points, public interfaces. Stack-appropriate (see [`../stacks/`](../stacks/)).
4. **Data model.** Entities, relationships, invariants. Migrations to be generated.
5. **Boundaries.** HTTP endpoints consumed/exposed, queue messages, filesystem, clock, randomness, third parties — each with a link to its contract.
6. **Error model.** Exception types/codes and how they surface to users.
7. **Observability.** Logs, metrics, traces. Correlation id propagation if applicable.
8. **Security.** Authn, authz, input validation, secrets handling. References ADRs if decisions are non-local.
9. **Test strategy.** Which layers, which runners, which hermetic environments (docker-compose, testcontainers, in-memory). Criteria for integration vs. unit.
10. **Rollout.** Feature flag, migration order, compatibility windows. Required only when multi-deploy impact.

### `research.md`

One section per library/service investigated. Each entry:

```
## <library-name>@<version>

- captured: YYYY-MM-DD
- source: context7 / WebFetch / docs URL
- API shape relevant to this feature:
  - ...
- Gotchas / rate limits / versioning constraints:
  - ...
- Decision impact (ties to plan §N):
  - ...
```

Use `context7` first. Fall back to `WebFetch` only when context7 does not index the source. Every claim must have a source line.

### `contracts/`

Machine-readable shapes. Choose format per boundary type:

- **HTTP**: `openapi.yaml` (preferred) or `http.yaml` with endpoint/method/params/responses.
- **Queue**: JSON schema of the message payload.
- **Filesystem**: directory layout description with path patterns and file formats.
- **Internal boundary**: typed interface in the stack's language, placed as `contracts/<name>.ts|.py|.php|.rs|.go`.

Every contract has a `version` field and a `captured` date. A contract is **owned** by the code that defines it and **consumed** by the code that calls it. Contract tests exist for both sides when possible.

### ADRs

Open an ADR from [`../templates/adr.md`](../templates/adr.md) when:

- A decision affects more than one task.
- A decision amends the constitution.
- A decision introduces a new dependency, runtime, or service.
- A decision chooses between two non-obvious alternatives.

## Agent procedure

1. Read `spec.md`, constitution, existing code (if brownfield).
2. Spawn the `Plan` subagent for medium/large features; draft inline for small. See [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md).
3. Research:
   - For every library that will be touched, query `context7`. Write `research.md` entries.
   - For every third-party integration, fetch the docs. Record rate limits, auth, pagination.
4. Draft `plan.md` using the template.
5. Draft contracts. At least one per external boundary.
6. Write ADRs as needed.
7. Present to the user in Spanish, highlighting tradeoffs. Wait for approval.
8. Commit: `plan: NNN initial plan + research + contracts`.

## Quality checks

- Every stack decision traces to research or constitution.
- Every external boundary has a contract.
- Every data-migrating change lists the migration and rollback.
- Research entries are <60 days old (re-query otherwise).
- The plan's test strategy names concrete layers and tools from the stack appendix.
- No decision invents a boundary the constitution disallows (e.g., introducing a new service without declaring it in Article: Boundaries → requires amendment).

## Slash command

`/sdd-plan`. See [`../commands/sdd-plan.md`](../commands/sdd-plan.md).

## Cross-references

- Template: [`../templates/plan.md`](../templates/plan.md), [`../templates/research.md`](../templates/research.md), [`../templates/adr.md`](../templates/adr.md), [`../templates/contracts.md`](../templates/contracts.md)
- Checklist: [`../checklists/plan.md`](../checklists/plan.md)
- Context7 usage: [`../tools/mcp-context7.md`](../tools/mcp-context7.md)
- Subagents: [`../tools/claude-code-subagents.md`](../tools/claude-code-subagents.md)
- Anti-patterns: [`../anti-patterns.md`](../anti-patterns.md) (`Stale research`, `Contracts-as-afterthought`)
