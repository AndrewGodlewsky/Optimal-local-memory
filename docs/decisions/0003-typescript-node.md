# 0003: Build in TypeScript/Node, run with `npx`

**Status:** Accepted (user), 2026-10-20

## Context

The MCP server has to start easily on every developer's machine, from
VS Code Copilot and from Claude Code, without per-person setup.

## Decision

Write the MCP server (and its CLI tools) in **TypeScript on Node.js**,
launched with `npx`.

## Consequences

- Matches how most MCP servers are distributed; both harnesses start
  `npx` servers with a short config entry.
- Developers need Node.js installed. Most already have it for VS Code
  tooling; this should still be stated in setup docs.
- The official MCP TypeScript SDK can be used.
- Which Node version to require, and how the package is published or
  vendored into each repo, are still **open**.
