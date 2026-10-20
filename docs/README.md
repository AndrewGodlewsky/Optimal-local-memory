# Documentation

Start with [../CONTEXT.md](../CONTEXT.md) for the short version. These docs
go deeper.

**Status labels used throughout:**
- **Accepted (user):** decided by the project owner.
- **Proposed (agent call):** suggested by an agent, not yet confirmed. Open
  to change.
- **Open:** not decided yet.

## Background

- [The OptChat gist, explained](background/optchat-gist-explained.md): the
  design that inspired this project, step by step, and what we take from it.

## Decisions

Architecture decision records (ADRs). One file per decision; never rewrite
an accepted one. Supersede it with a new record instead.

| # | Decision | Status |
|---|---|---|
| [0001](decisions/0001-repo-local-branch-aware-memory.md) | Memory lives in the repo and travels with branches | Accepted (user) |
| [0002](decisions/0002-mcp-as-integration-point.md) | An MCP server is the shared integration point | Accepted (user) |
| [0003](decisions/0003-typescript-node.md) | Build in TypeScript/Node, run with `npx` | Accepted (user) |
| [0004](decisions/0004-distilled-memories-over-transcripts.md) | Distilled memories are the product; transcripts are evidence | Accepted (user) |
| [0005](decisions/0005-code-and-process-anchors.md) | Memories are anchored to code locations and processes | Accepted (user) |
| [0006](decisions/0006-agent-drafts-human-reviews.md) | Agents draft memories; humans review them in PRs | Accepted (user) |
| [0007](decisions/0007-memory-supplements-skills.md) | Memory supplements skills and helps them evolve | Accepted (user) |
| [0008](decisions/0008-search-then-zoom-retrieval.md) | Layered retrieval: digest, search, zoom | Proposed (agent call) |

## Architecture

- [Overview](architecture/overview.md): the parts and how they fit.
- [Memory model and storage](architecture/storage.md): what a memory is and
  where it lives on disk.
- [Retrieval](architecture/retrieval.md): how agents get the right slice of
  memory, and the draft MCP tools.
- [Capture](architecture/capture.md): how memories get written, per harness.

## Risks

- [Pitfalls](pitfalls.md): problems to design for, with candidate
  mitigations.

## Design and plans

- `superpowers/specs/`: the design document (not yet written).
