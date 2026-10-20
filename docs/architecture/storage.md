# Memory model and storage

**Status:** Proposed (agent call). Everything on this page is a starting
point for the design document, not a decision.

## A memory

One Markdown file with a small YAML header. Example:

```markdown
---
id: 01JAX7Q2M3V9K8T4R6N5P0B1C2
kind: lesson            # decision | lesson | knowledge | process
title: Never retry Stripe webhooks on 4xx
anchors:
  paths: [src/billing/webhook.ts, src/billing/retry/]
  processes: [payments-change]
status: active          # active | superseded | retired
supersedes: []
author: bob
created: 2026-10-14
evidence: [session:01JAX6…]
---

Retrying a 4xx response re-sends the charge request, and Stripe treats it
as a new charge: customers were double-billed in staging (see evidence).
Only retry on 5xx and network errors. **Why:** decided by Bob after the
staging incident; idempotency keys alone didn't prevent it because …
```

Why this shape:
- **Readable in a PR diff**, so review is easy ([0006](../decisions/0006-agent-drafts-human-reviews.md)).
- **One memory per file** with a globally unique id (ULID), so parallel
  branches never conflict.
- **The header is the index:** kind, anchors and status are what search
  filters on.
- **Short body, with the reason.** A size limit (the gist's 512 bytes is a
  candidate, perhaps more for decisions) keeps memories focused.

## On disk

```
.memory/
  memories/
    01JAX7Q2…-never-retry-stripe-4xx.md
    01JAX8B1…-legacy-orm-leave-until-rewrite.md
  evidence/
    <author>/<session-ulid>.jsonl      # if transcripts are committed (open)
  processes.md                          # shared list of process names (open)
.gitignore → .memory/.index/            # local search index, never committed
```

## Changing and retiring memories

- Memories are **not edited in place once merged**. A correction is a new
  memory that `supersedes` the old one; the old one's status becomes
  `superseded`. This keeps history and avoids two branches editing the
  same file.
- A memory is `retired` when it no longer applies (code removed, lesson
  folded into a skill).
- Whether status changes are edits to the old file (small, rare conflicts)
  or derived from `supersedes` links (no edits at all) is **open**.

## Evidence

Evidence is whatever a memory can link back to for the full story. Options,
all **open**:

1. **Commit nothing extra:** link to the commit and PR. Smallest, but the
   reasoning in the conversation is lost.
2. **Commit summarized sessions:** the gist's 512-byte tree for each
   session. Medium size, zoomable.
3. **Commit raw transcripts** (clipped tool output) plus summaries. Most
   complete; biggest; highest secret-leak risk.

## Open questions

- Size limit per memory kind.
- How evidence is stored (above).
- How the process name list is kept and enforced.
- How to detect anchors pointing to renamed or deleted paths.
