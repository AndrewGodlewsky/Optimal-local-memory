# 0010: How the memory map is delivered

**Status:** Accepted (user), 2026-10-20. Details how the map from
[0009](0009-memory-map-instead-of-line-review.md) reaches people.

## Context

The user wants the memory map to be interactive: click into individual
memories, then zoom back out to see the whole picture. GitHub PR comments
render Mermaid diagrams in a sandboxed frame. `click` links on nodes are
blocked, and pan, zoom and full-screen controls are unconfirmed (see
[research](../research/2026-10-20-harness-capabilities.md)). A comment is also capped at about 65,000
characters, and Mermaid becomes unreadable at a few hundred nodes, so the
whole memory tree won't fit there for long.

## Decision

Split the job by scale:

1. **PR comment: this PR's changes.** A GitHub Action posts one comment
   per PR and updates it on every push. It contains:
   - a one-line summary (e.g. "+3 memories (2 lessons, 1 knowledge), 1
     superseded, touching `src/billing/` and `release`"),
   - a Mermaid diagram of only the changed parts of the memory tree,
   - a collapsible list of each changed memory (kind, title, first line)
     linked to its file in the PR. This is how you click into a memory
     from the PR.
2. **Local interactive map: the whole tree.** A command (draft name:
   `memory map --open`) generates one self-contained HTML file and opens
   it in the browser: the whole memory tree, the current branch's changes
   highlighted, expand and collapse, click into any memory and its
   evidence.
3. **Optional, not v1:** the Action also attaches the same HTML to the PR
   as a workflow artifact.

## Consequences

- The PR view stays small and readable because it only shows changes.
- The whole-tree view scales to thousands of memories, because it's a
  real page, not a comment.
- Both views come from the same generator, so the map logic lives in one
  place.
- Needs: a GitHub Action, permission to comment on PRs, and a way to
  compare memories between the PR branch and its base.
