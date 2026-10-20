# 0004: Distilled memories are the product; transcripts are evidence

**Status:** Accepted (user), 2026-10-20

## Context

The OptChat gist treats the full chat log, compressed into a summary tree,
as the memory. For a team, the valuable part isn't the chat itself. It's
what the chat produced: decisions, the reasons behind them, lessons and
repo knowledge. Git history already records *what* changed.

## Decision

The main unit of memory is a **distilled memory**: a short, focused record
of one of four kinds:

1. **Decision:** what was decided, and why.
2. **Lesson:** what failed or nearly failed, and why; traps and exceptions.
3. **Knowledge:** repo-specific facts the code doesn't show (conventions,
   setup quirks, how parts fit, legacy oddities).
4. **Process context:** what this repo needs on top of a general skill.

Transcripts and their summaries are kept as **evidence**: a memory can link
to the sessions it came from, and an agent can zoom into them when it needs
the full story.

## Consequences

- What agents see first is small and high-value.
- The gist's summary tree shrinks in role: it's used *within* a session's
  evidence, not as one team-wide timeline.
- Whether transcripts are committed at all, and in what form (raw, clipped
  or summarized), is still **open**. See [storage](../architecture/storage.md).
- The quality of distillation decides the quality of the system. The
  drafting prompt is a key piece of design work.
