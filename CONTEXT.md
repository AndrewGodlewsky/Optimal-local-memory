# Context

What this project is trying to achieve, why, and what has been decided so
far. Keep this file current: when a decision changes, update it here.
Deeper explanations live in [docs/](docs/README.md).

Decisions are marked with who made them:
**(user)** = decided by the project owner; **(agent call)** = proposed by an
agent and not yet explicitly confirmed, so open to change.

## Objective

Build a **repo-local memory system for AI coding agents** that a team of
developers shares through git. It should give any agent working in the repo
**exactly the context it needs** for its task: enough to avoid repeating
mistakes or undoing deliberate decisions, without flooding it with
irrelevant history.

It is especially aimed at complex and legacy codebases, where the important
intricacies are not visible from the code itself.

## Background

Inspired by Victor Taelin's design note
[OptChat / UniiChat: one chat that never ends](https://gist.github.com/VictorTaelin/91837951a5ce5b38f341ec1ba1df6449):
an append-only log of every message, compressed by a cheap model into a
binary tree of ≤512-byte summary lines, with a fixed-size "view" of that
tree and a `zoom` tool that drills from a summary down to the original
message.

That design assumes one person, one never-ending chat, and an agent loop you
fully control. None of those hold here, so we keep its best ideas and change
the rest:

| Keep from the gist | Change for this project |
|---|---|
| Append-only, verbatim records as evidence | Transcripts are the evidence layer, not the main product |
| Dense ≤512-byte summaries written with clear priorities (the user's words first, never overstate progress) | Distilled memories (decisions, reasons, lessons, repo knowledge) are the main product |
| `zoom` from summary to source | Retrieval is "search to find, zoom to read", not one chronological view |
| | Organized by code location and process, not one team-wide timeline |
| | Pull-based through MCP, because we don't control the agent loop in Copilot or Claude Code |

## Requirements

- **Repo-local and branch-aware (user).** Memories are committed to the repo
  and travel with branches. Anyone who pulls a branch gets the memories
  written on it. Merging a branch merges its memories.
- **Multi-harness (user).** Must work with GitHub Copilot (VS Code now, other
  Copilot harnesses later) and Claude Code. The core must not depend on any
  one harness's features.
- **Team scale (user).** 5–10 developers contributing to the same memory.
- **Sharing is fine (user).** Transcripts and summaries may be shared with the
  team; privacy is not a constraint. (Secret leakage still matters; see
  pitfalls.)
- **Right-sized context (user).** Neither too much nor too little; the
  measure of success is whether agents achieve the intended outcome more
  often.

## What memory holds (user)

All of it specific to this repo:

1. **Decisions and their reasons:** why it is built this way, why a feature
   was implemented as it was.
2. **Lessons:** what failed and why, traps, near-misses, exceptions.
3. **Repo knowledge:** conventions, setup quirks, how the parts fit,
   legacy oddities. What the code alone does not reveal.
4. **Process context:** what this repo needs on top of a general skill.

Git history already records *what* changed. Memory records *why*, and *what
was learned*.

## Anchors: how memory is tied to the work (user)

- **Code anchors:** files, folders, modules (e.g. `src/billing/`).
- **Process anchors:** named, repeatable workflows the repo has (e.g.
  `release`, `db-migration`, `new-api-endpoint`).

`AGENTS.md` can then stay short: it names a process, and memory supplies the
accumulated context for it.

## Relationship to skills (user)

- Skills are the **"how"**: procedures, often general and reused across repos.
- Memory is the **repo-specific supplement**: lessons, exceptions, and local
  knowledge that make a general skill work correctly in *this* repo.
- Memory should help skills **evolve**: when the same lesson recurs, propose
  folding it into the skill, then retire the memory. (The recurrence
  mechanism is an agent call; the goal is the user's.)

## Capture and review (user)

At the end of a task, the agent **drafts memories automatically** as files
in the branch. The developer sees them in their diff, and the team reviews
them in the PR like code. Nothing becomes shared memory without a human
seeing it, but nobody has to remember to write it.

## Retrieval direction (agent call, discussed and accepted in direction)

Layered, so each layer is small and only the relevant slice is loaded:

1. **Always loaded (a few KB):** a digest of the most important decisions and
   conventions.
2. **Found by search:** `memory_search(query, paths?, process?)` over
   memories and summaries, ranked, returning a handful of short results.
3. **Read by zoom:** `zoom(id)` from a memory or summary down to its
   evidence (transcript excerpts, sessions).

Main known risk: agents not calling the memory tools at all. Mitigations
include firm rules in `AGENTS.md`, a cheap start-of-task tool, skills that
point into memory, and a retrieval eval set of real questions.

## Technology

- **MCP server in TypeScript/Node, run with `npx` (user).** MCP is the one
  integration point every target harness supports.
- **Memories as Markdown files with a small metadata header (agent call),**
  so they read cleanly in PR diffs.

## Known pitfalls to design for

- **Merge conflicts:** many people and branches writing memory at once. Use
  one file per memory or session and globally unique ids; avoid shared
  index files that everyone edits.
- **Staleness:** memory says X, but the code has changed. The code wins;
  memories need a way to be marked outdated or retired.
- **Abandoned branches:** memories written on branches that never merge
  should not leak into `main`.
- **Untrusted text:** a memory is text injected into an agent's prompt.
  Treat it as data, never instructions; PR review is the main guard.
- **Secrets in transcripts:** tool output can contain tokens and env vars.
  Scan before committing.
- **Repo bloat:** raw transcripts grow fast. Decide what is committed and
  in what form.
- **Too much / too little context:** needs measuring, not guessing.
- **Agents not using it:** see retrieval risk above.
- **Uneven capture across harnesses:** Claude Code has rich hooks; Copilot
  harnesses have fewer automatic capture points.

## Open questions

- Anti-goals: what would make this a failure even if it technically works?
- What exactly is committed: distilled memories only, or also transcripts or
  summaries? In what form?
- How a memory is retired or marked superseded.
- How the always-loaded digest is built and kept small.
- Search approach: keyword and path matching first; embeddings only if
  needed?
- Scope of the first version.

## Status

**Phase: design (as of 2026-10-20).** No product code yet. Next steps:

1. Finish the design conversation and write the design document in
   `docs/superpowers/specs/`.
2. Get the design approved.
3. Write the implementation plan, then build.
