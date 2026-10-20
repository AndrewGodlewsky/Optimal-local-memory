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
developer sees them in their diff (keeps, edits or deletes)
      │
      ▼
PR review: team reviews memories alongside the code
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

| Harness | Mechanism (draft) | Reliability |
|---|---|---|
| Claude Code | `Stop` or `SessionEnd` hook prompts the agent to draft memories; hooks can also log the session as evidence | High |
| Copilot in VS Code | Instruction: "before finishing, call `memory_draft` for anything worth keeping"; possibly a prompt file the developer runs | Medium: depends on the model following it |
| Other Copilot harnesses (CLI, coding agent) | Same instructions; any hooks they offer | Varies; to verify per harness |

## Evidence capture

If session evidence is kept ([storage](storage.md), open), it needs a
transcript. Claude Code hooks expose the transcript path. Copilot harnesses
store chat history differently, and what's accessible needs research.

## Open questions

- Exactly when "end of task" is, in harnesses without hooks.
- How to avoid duplicate memories (the same lesson drafted twice by two
  people).
- Whether the drafting should be done by the working agent, or by a
  separate cheap-model step over the session.
- Review guidance: what reviewers should check, and when to reject.
