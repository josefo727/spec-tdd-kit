# MCP — context7

**`context7`** is the MCP server used for fetching up-to-date documentation for libraries, frameworks, SDKs, and CLI tools. The kit requires it during the `plan` and `implement` phases whenever code touches an external library.

## Why mandatory

LLM training data lags. Library APIs drift. `research.md` entries written from memory decay silently. `context7` queries the provider's current docs (indexed by the MCP server) and returns grounded snippets.

## Installation

If not already present, check the user's `~/.claude/settings.json` or `~/.claude.json` for `plugin:context7:context7` tools. The kit's `AGENTS.md` references the tool names:

- `mcp__plugin_context7_context7__resolve-library-id`
- `mcp__plugin_context7_context7__query-docs`

Verify availability in a session by asking the model to list its tools or by observing context7 tool calls working.

## When to use

- Any time the plan picks a new library or bumps a major version.
- Any time a `research.md` entry is older than 60 days.
- Any time a test reveals a behavior inconsistent with the agent's assumption.
- Any time the user asks about a library by name.

## When NOT to use

- Business logic refactors (no external API involved).
- Bug fixes that do not depend on library behavior.
- Code review of pure domain code.
- General programming concepts.

## Query patterns

1. **Resolve the library** (map friendly name to a `context7`-indexed library id):

   ```
   tool: mcp__plugin_context7_context7__resolve-library-id
   args: { libraryName: "vuetify" }
   ```

2. **Query docs** for a topic:

   ```
   tool: mcp__plugin_context7_context7__query-docs
   args: {
     libraryId: "<resolved id>",
     query: "<task-specific question — e.g., 'server-side pagination props on v-data-table'>",
     tokens: 2000
   }
   ```

3. **Record in `research.md`** with `source: context7` and the exact `libraryId` for reproducibility.

## Prompt style for Claude Code

Inside a `plan` phase prompt:

```
Before recommending <library>, call context7:
1) resolve-library-id for "<library>"
2) query-docs with a short, task-specific query
Then write a research.md entry with the findings and proceed with the plan.
```

## Failure modes

- **Library not indexed.** Fall back to `WebFetch` on the official docs URL. Note in `research.md` that context7 did not index this library.
- **Docs conflict with published behavior.** Note both and open an ADR if the decision depends on it.
- **Rate limiting.** Slow down and batch queries per library.

## Related skill

The `context7-auto-research` skill (installed by the user) proactively invokes context7 on user questions about libraries. The kit does not rely on it — prefer explicit queries during `plan` — but it can shorten research when the user is exploring.

## Cross-references

- Plan phase: [`../phases/03-plan.md`](../phases/03-plan.md)
- Research template: [`../templates/research.md`](../templates/research.md)
- MCP catalog: [`./mcp-catalog.md`](./mcp-catalog.md)
