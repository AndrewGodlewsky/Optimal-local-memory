# 0002: An MCP server is the shared integration point

**Status:** Accepted (user), 2026-10-20

## Context

The team uses GitHub Copilot in VS Code today and may use other Copilot
harnesses later; Claude Code must work too. Each harness has its own
hooks, transcript formats and extension points. We don't control the agent
loop in any of them.

## Decision

The memory system's core is a **local MCP server**. Every harness talks to
it the same way, through tools. Harness-specific code (capture hooks,
instruction files, skills) is kept in **thin adapters** around it.

## Consequences

- One implementation of storage, search and zoom serves every harness.
- Retrieval is **pull-based**: the agent has to call the tools. Getting
  agents to reliably do that is a core design problem (see
  [retrieval](../architecture/retrieval.md) and [pitfalls](../pitfalls.md)).
- Capture quality will vary by harness, because automatic hooks differ
  (see [capture](../architecture/capture.md)).
- Tool names, descriptions and output formats become a public interface
  and need to be designed carefully: they *are* the prompt the agent sees.

## Alternatives considered

- **A skill only:** portable packaging, but nothing is captured or
  retrieved unless the model decides to, and no shared search.
- **Instruction files only** (`AGENTS.md`): every harness reads them, but
  they're hand-maintained, flat and don't scale.
- **Our own agent loop** (faithful to the gist): would mean giving up
  Copilot and Claude Code. Fails the multi-harness requirement.
