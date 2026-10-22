# 0009: PRs show a memory map instead of line-by-line review

**Status:** Accepted (user), 2026-10-20. Amends
[0006](0006-agent-drafts-human-reviews.md): agents still draft memories
automatically, but the review step changes.

## Context

Decision 0006 had the team review every memory in the PR like code. In
practice, reading many small memory diffs is tedious, and teams would soon
rubber-stamp them, one of the project's anti-goals. What the team actually
needs is to see **what parts of memory a change grew**, not to proofread
each entry.

## Decision

- Each PR shows a **node graph of the memories it adds, supersedes or
  retires, and where they attach**: code anchors (folders, files) and
  process anchors.
- The team reads the graph for a high-level picture; reading individual
  memories is optional.
- We accept **trusting the summarization** of the memories themselves.
- Auditing memory quality and relevance is future work
  ([future.md](../future.md)).

## Consequences

- Review cost stays low, which protects against the "it becomes a chore"
  anti-goal.
- PR review is **no longer the main guard** against wrong, noisy or
  injected memories. More weight falls on:
  - the drafting prompt and validation at draft time (size, kind, anchors,
    duplicates),
  - framing memory as data, never instructions, in every tool response,
  - evidence links, so a doubtful memory can be checked.
- The graph needs a generator, and a place to show it in the PR. See
  [0010](0010-memory-map-delivery.md).
- Memories are still committed files, so anyone *can* read them in the
  diff.
