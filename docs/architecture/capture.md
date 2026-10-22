# Capture

**Status:** The flow is accepted (user,
[0006](../decisions/0006-agent-drafts-human-reviews.md)); the mechanisms
per harness are **Proposed (agent call)**.

## The flow

```
agent works on a task
      │
      ▼
end of task: agent drafts memories  ──►  .memory/memories/*.md on the branch
      │
      ▼
developer can see them in their diff (optional: keep, edit or delete)
      │
      ▼
PR: a memory map shows what was added and where (decision 0009)
      │
      ▼
merge: memories become shared on the target branch
```

## What the agent should draft

From the drafting prompt (to be written), in priority order, borrowing the
gist's priorities:

1. **The user's words:** decisions, corrections and the reasons given,
   kept close to verbatim.
2. **Lessons:** what failed and why; anything surprising.
3. **Repo knowledge** discovered along the way that the code doesn't make
   obvious.
4. **Process context:** anything a future run of the same process needs.

And what it should **not** draft: what the diff or commit message already
says, temporary task state, guesses presented as facts, and anything
that makes work look further along than it is.

## How capture is triggered, per harness

Based on [research](../research/2026-10-20-harness-capabilities.md):

| Harness | Mechanism (draft) | Reliability |
|---|---|---|
| Claude Code | `Stop` / `SessionEnd` hooks prompt drafting; event hooks can log the session as evidence (config in `.claude/settings.json`) | High |
| Copilot in VS Code | Instructions to call `memory_draft`, plus hooks (preview) from `.github/hooks/*.json`: `Stop`, `PostToolUse`, `UserPromptSubmit`. No `SessionEnd` | Medium–high |
| Copilot CLI | Same `.github/hooks/*.json`; has `sessionEnd` and `agentStop` | Medium–high |
| Copilot coding agent | Same `.github/hooks/*.json`; ephemeral VM, so anything kept must be committed in the job; MCP is tools-only and set in repo settings | Medium |

## Evidence capture

If session evidence is kept ([storage](storage.md), open), every target
harness can supply it: hooks receive a transcript path and per-event
fields. All vendors call their transcript formats unstable, so the safer
route is to build our own normalized event log from hook fields (prompt,
tool name, args, result), close to the gist's own log format. See the
[evidence options](../explorations/evidence-options.html).

## Open questions

- Exactly when "end of task" is, in harnesses without hooks.
- How to avoid duplicate memories (the same lesson drafted twice by two
  people).
- Whether the drafting should be done by the working agent, or by a
  separate cheap-model step over the session.
- Review guidance: what reviewers should check, and when to reject.
