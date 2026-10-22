# 0006: Agents draft memories; humans review them in PRs

**Status:** Accepted (user), 2026-10-20. **Amended by
[0009](0009-memory-map-instead-of-line-review.md):** the team sees a memory
map in each PR instead of reviewing every memory line by line.

## Context

If people have to remember to write memories, they won't. If agents write
memories with no oversight, memory fills with noise, mistakes or injected
instructions.

## Decision

At the end of a task, the agent **drafts memories automatically** as files
in the working branch. The developer sees them in their diff, and the team
reviews them in the PR like code. Nothing becomes shared memory without a
human seeing it.

## Consequences

- Capture costs developers nothing beyond reading a diff.
- PR review is the main defence against wrong, noisy or malicious memories.
- Memories must be **easy to review**: short, readable Markdown, one per
  file, with clear anchors and reasons.
- Reviewers need guidance on what makes a good memory, and when to reject
  one.
- Memories exist on a branch before review. Agents on that branch will see
  unreviewed memories; whether to mark or treat them differently is
  **open**.
- How the "end of a task" is detected differs by harness (see
  [capture](../architecture/capture.md)).
