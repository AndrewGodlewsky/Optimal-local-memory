# 0005: Memories are anchored to code locations and processes

**Status:** Accepted (user), 2026-10-20

## Context

The goal is to give an agent exactly the context it needs. That requires
knowing which memories are relevant to the task at hand.

## Decision

Every memory can carry two kinds of anchor:

- **Code anchors:** paths or globs (`src/billing/`, `legacy/**/*.sql`).
- **Process anchors:** names of repeatable workflows the repo has
  (`release`, `db-migration`, `new-api-endpoint`).

A memory may have several anchors, or none (repo-wide knowledge).

## Consequences

- Retrieval can filter by where the agent is working, and what it's doing.
- Code anchors can often be filled in automatically from the files a
  session touched.
- `AGENTS.md` can stay short: it names a process, and memory supplies the
  accumulated context for it.
- Anchors go stale when code moves. Renamed or deleted paths need
  detecting (see [pitfalls](../pitfalls.md)).
- Process names need a shared vocabulary, or the same process ends up
  under several names. How that list is kept is **open**.
