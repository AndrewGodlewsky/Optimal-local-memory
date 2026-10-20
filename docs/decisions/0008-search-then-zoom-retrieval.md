# 0008: Layered retrieval: digest, search, zoom

**Status:** Proposed (agent call), 2026-10-20. The direction was discussed
and agreed with the user; the details are not final.

## Context

The OptChat gist retrieves by showing the agent a chronological map of the
whole history and letting it zoom. For a team this breaks down:

- Neighbouring messages in time come from unrelated work, so merged
  summaries turn to mush.
- At team volume, old history gets so compressed that finding something
  takes many zoom steps.
- Recent activity by others isn't what a developer needs; relevant
  history about their area is.
- We can't inject a large view on every call in Copilot or Claude Code.

## Decision

Three layers, each small:

1. **Always loaded (a few KB):** a digest of the most important decisions
   and conventions.
2. **Found by search:** ranked results filtered by code and process
   anchors.
3. **Read by zoom:** from a memory down to its evidence.

"Search to find, zoom to read."

## Consequences

- Each layer can be sized and measured on its own.
- Search quality becomes central. Start with keyword and anchor matching;
  add embeddings only if measurements show it's needed.
- The search index is derived data: built locally from committed files,
  never committed (so it never conflicts).
- The biggest risk is agents not calling the tools. See
  [retrieval](../architecture/retrieval.md).

## Alternatives considered

- **The gist's single chronological view:** see the context above.
- **Embeddings-only (vector) search:** weak on exact names, paths and
  error strings, which matter most in code; adds dependencies.
