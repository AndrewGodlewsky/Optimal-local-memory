# Architecture overview

**Status:** Early. The parts and their roles are agreed in direction; the
details in the linked pages are mostly **Proposed (agent call)**.

## The parts

```
 ┌─ GitHub Copilot (VS Code, other harnesses) ─┐   ┌─ Claude Code ─┐
 │  AGENTS.md / instructions                   │   │  CLAUDE.md    │
 │  skills (.github/skills)                    │   │  skills, hooks│
 └──────────────────────┬──────────────────────┘   └──────┬────────┘
                        │      thin adapters              │
                        └───────────── MCP ───────────────┘
                                        │
                        ┌───────────────▼──────────────────┐
                        │  memory MCP server (TypeScript)  │
                        │  start · search · get · zoom     │
                        │  draft · supersede               │
                        └───────────────┬──────────────────┘
                                        │ reads / writes files
                        ┌───────────────▼──────────────────┐
                        │  .memory/ in the repo (committed)│
                        │  memories/  one file per memory  │
                        │  evidence/  session records      │
                        └───────────────┬──────────────────┘
                                        │ derived, local only
                        ┌───────────────▼──────────────────┐
                        │  local index (not committed)     │
                        │  search index · digest cache     │
                        └──────────────────────────────────┘
```

| Part | Role | Status |
|---|---|---|
| Instruction files | Tell agents the memory exists and when to use it | Accepted (user) |
| Skills | The "how" for processes; point into process memory | Accepted (user) |
| Adapters | Harness-specific capture and nudges | Proposed (agent call) |
| MCP server | Single implementation of retrieval and writing | Accepted (user) |
| `.memory/` files | The memory itself, versioned with the code | Accepted (user); layout Proposed |
| Local index | Fast search; rebuilt from files, never committed | Proposed (agent call) |

## The two main flows

**Retrieval** ([details](retrieval.md)): the agent starts a task, gets a
small digest, searches by topic, path or process, and zooms into evidence
when it needs the full story.

**Capture** ([details](capture.md)): during or at the end of a task, the
agent drafts memories into `.memory/` on the current branch; the developer
sees them in the diff; the team reviews them in the PR; merging makes them
shared.

## Design principles

- **Git is the database.** Everything that matters is a committed file;
  everything else can be rebuilt.
- **One file per memory.** Parallel writers never touch the same file.
- **Small by default.** Every response the server returns has a size budget.
- **Harness-agnostic core.** Anything harness-specific lives in an adapter.
- **Code wins.** When memory and code disagree, the code is the truth and
  the memory is stale.
- **Memory is data.** Never instructions to obey.
