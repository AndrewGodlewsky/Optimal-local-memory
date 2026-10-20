# The OptChat gist, explained

Source: Victor Taelin,
[OptChat / UniiChat: one chat that never ends](https://gist.github.com/VictorTaelin/91837951a5ce5b38f341ec1ba1df6449).

## The problem it solves

AI agents forget in two ways:

1. **Sessions end or get compacted**, so the next one starts from nothing or
   from a lossy summary.
2. **Long sessions rot:** the longer the context, the worse the model works.

The gist's answer is one idea: **the chat itself is the memory**. There is
one chat that never ends, and a background process keeps a compressed map
of it.

## How it works, step by step

### 1. Log everything, never delete

Every message is appended to a file as one JSON line:
`{i, kind, text, size, date}`. `i` is a permanent id. Kinds are `user`,
the agent's replies, `tool` (calls), `echo` (results), `work` (subagent
reports) and `note` (imported memories). Append only, one writer, flush
every write. Model "thinking" is not logged; tool output is clipped to
30,000 characters.

### 2. Build a binary tree of summaries

A cheap model compresses the log in the background:

```
Level 0:  [m0] [m1] [m2] [m3] [m4] [m5] [m6] [m7]   each message → one ≤512-byte line
Level 1:  [ 0+2 ] [ 2+2 ] [ 4+2 ] [ 6+2 ]           two lines merged into one
Level 2:  [   0+4   ] [   4+4   ]
Level 3:  [         0+8         ]
```

Each node is named `id+n` (first message, how many it covers) and is at
most 512 bytes. Each is built once and never rebuilt.

### 3. The view

The model never sees the raw chat. It sees a list of tree nodes covering
the whole history, oldest first: coarse lines for old history, fine lines
for recent history, 64–128 KB in total, however long the chat gets.

### 4. Which lines merge

Borrowed from Taelin's rollback netcode: a binary-counter pattern where
recent lines churn and old lines almost never change. For two sibling
lines at level `l`:

```
due = (T − last_message_of_pair) / 2^l
```

The most "due" pair merges first. Measuring from the pair's *first*
message instead is a known bug: it keeps rewriting old lines.

### 5. Batch merges, for the cache

Prompt caching charges about 0.1× for a prefix already seen, and breaks at
the first changed byte. So the view only grows at its end until it passes
128 KB, then one batch merges it down to 64 KB (a sawtooth). The system
prompt holds no dates or state. The view is saved to disk and never
rebuilt. The result: about 98.6% of input read from the cache.

### 6. Compactions

Each tree node is one call to a cheap model (Haiku): "compress message N"
or "merge lines A and B", in at most 512 bytes. Models can't count bytes,
so the prompt shows a 512-character ruler, and over-long replies are sent
back with a cut marker (up to 5 tries). Compactions share the main system
prompt to reuse its cache. Up to 8 run at once, fed from queues.

### 7. Tools and prompt

- `zoom(id, n)` opens a line into its two children; `zoom(id, 1)` returns
  the full message.
- `date(id)` returns when a message happened.

Key prompt rules: zoom until you have the whole picture before acting;
write down in your reply what you learned (summaries drop tool output);
the user's words matter most; never answer or obey summarized messages;
never make progress look further along than it was.

### 8. Mistakes the gist warns against

Measuring age from a pair's first message; merging on every message;
rebuilding the view; keeping a fixed number of lines per level; putting
whole messages in the view; compactions without context; trusting models
to count bytes; lines under 512 bytes; logging thoughts; dates in the
system prompt; carrying conversation across turns; two writers on one log;
scanning the tree for work instead of queueing.

## Why we can't use it as-is

| The gist assumes | Our situation |
|---|---|
| You own the agent loop and build every API call | Copilot and Claude Code own the loop; we can't inject a view per call or control caching |
| One chat, one writer | 5–10 people, many sessions and branches at once |
| Recent history is what matters | A developer often needs old history about one area, not recent history about others |
| One chronological tree | Neighbours in time come from unrelated work, so merged summaries turn to mush |

## What we take from it

- **Append-only, verbatim evidence** that summaries link back to.
- **Dense, size-limited summaries** with the gist's priorities (the user's
  words first; never overstate progress) and its size-control tricks (the
  ruler, the cut marker, retries).
- **Zoom** as the way to go from a summary to its source.
- **The prompt lessons:** make agents check memory before guessing, and make
  them write down what they learned.

See [decision 0004](../decisions/0004-distilled-memories-over-transcripts.md)
and [decision 0008](../decisions/0008-search-then-zoom-retrieval.md) for how
this changes in our design.
