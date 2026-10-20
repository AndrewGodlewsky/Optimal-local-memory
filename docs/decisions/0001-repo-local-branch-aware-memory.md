# 0001: Memory lives in the repo and travels with branches

**Status:** Accepted (user), 2026-10-20

## Context

A team of 5–10 developers works in one repository. Memory needs to be
available to everyone, and to match the state of the code they're looking
at.

## Decision

Memories are files committed to the repository. They behave like code:
- Pulling a branch gives you the memories written on that branch.
- Merging a branch merges its memories into the target.
- Memories can be reviewed, reverted and blamed like any other file.

## Consequences

- No server, database or account is needed; git is the sync mechanism.
- Memory always matches the code it describes, branch by branch.
- Memories on abandoned branches never reach `main` (they're dropped with
  the branch).
- The file layout must make **merge conflicts rare**: many people write
  memory in parallel. See [storage](../architecture/storage.md).
- Anything derived (search indexes, digests) should be rebuildable from the
  committed files, so it never needs merging.
- Repo size has to be watched, especially if transcripts are committed.

## Alternatives considered

- **Hosted memory service:** real-time sharing, but memory would no longer
  follow branches, and it adds infrastructure and auth.
- **Memory built only on `main` by CI:** removes conflicts but breaks the
  "pull a branch, get its memories" requirement.
