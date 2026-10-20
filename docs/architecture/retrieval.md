# Retrieval

**Status:** Direction accepted; tool designs are a **Proposed (agent call)**
first draft for discussion.

The goal: give the agent **exactly the context it needs**, not more and not
less. See [decision 0008](../decisions/0008-search-then-zoom-retrieval.md).

## The layers

| Layer | What | Size budget (draft) | When |
|---|---|---|---|
| 1. Digest | The most important active decisions and conventions | ~3–5 KB | Start of every task |
| 2. Search | Ranked memories matching the task's topic, paths and processes | ~5–10 results, ~4 KB | Before changing an area or running a process |
| 3. Zoom | A memory's full text, then its evidence | One item at a time | When a result is relevant but not detailed enough |

Each layer narrows what's loaded. An agent typically reads a few KB of
memory in total, not tens of KB.

## Draft MCP tools

Names and parameters are placeholders.

### Reading

- **`memory_start(task?, paths?, process?)`**
  Returns the digest plus the top few results for the task, if one is
  given. The cheapest possible "check memory first" call.
- **`memory_search(query, paths?, process?, kinds?, limit?)`**
  Returns ranked one-line results: `id | kind | title | anchors | first
  line`. Matches on keywords, anchor paths (including parent folders) and
  process names.
- **`memory_get(id)`**
  Returns one memory in full.
- **`memory_zoom(id)`**
  From a memory, opens its evidence (session summary lines, then
  transcript excerpts), the gist's zoom applied to evidence.

### Writing

- **`memory_draft(kind, title, body, anchors, evidence?)`**
  Writes a new memory file into `.memory/memories/` on the current branch.
  Validates size, kind and anchors.
- **`memory_supersede(old_id, new memory…)`**
  Writes a correction that replaces an older memory.

## Ranking (draft)

Rough order of signals:
1. Process anchor matches the task's process.
2. Path anchor matches a file being touched, or its folder.
3. Keyword matches in title and body (BM25 or similar).
4. Kind: decisions and lessons above general knowledge.
5. Status: active only, unless asked for history.

Embeddings only if an evaluation shows keyword and anchor search missing
things it should find.

## Getting agents to actually use it

This is the biggest risk. Candidate measures, likely all needed:

- **A short, firm rule** in `AGENTS.md` / Copilot instructions: "At the
  start of a task, call `memory_start`. Before changing code in an area,
  call `memory_search` with the paths you'll touch."
- **Tool descriptions that say when to call them**, since the description
  is part of what the model reads.
- **Skills that reference their process**, so using the skill pulls in its
  memories.
- **Claude Code hooks** that inject the digest at session start (an
  adapter, not core).

## Measuring "the right amount"

- **An eval set:** 20–30 real questions from the repo's history ("why don't
  we retry 4xx webhooks?"), each with the memory that answers it. Measure
  whether the agent finds it, and how many KB it read to get there.
- Track **precision** (how much retrieved memory was useful) as well as
  **recall** (whether the needed memory was found). Too much context shows
  up as low precision.

## Open questions

- Exact size budgets per layer.
- How the digest is chosen: a manual "pinned" flag, kind, or recency?
- How results show that a memory is unreviewed (on a branch, not merged).
- Whether search should look across other branches' memories.
