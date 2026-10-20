# 0007: Memory supplements skills and helps them evolve

**Status:** Accepted (user), 2026-10-20. The recurrence mechanism below is
a Proposed (agent call).

## Context

Both Copilot and Claude Code support skills (`SKILL.md`), and many skills
are general and reused across repos. A general skill doesn't know this
repo's quirks.

## Decision

- **Skills are the "how":** procedures, kept short and stable.
- **Memory is the repo-specific supplement:** lessons, exceptions and local
  knowledge that make a skill work correctly in *this* repo. It is handed
  to the agent alongside the skill, through process anchors
  ([0005](0005-code-and-process-anchors.md)).
- **Memory helps skills evolve.** *Proposed (agent call):* when the same
  lesson recurs across sessions, the system flags it and proposes folding
  it into the skill. Once the skill absorbs it, the memory is retired.

## Consequences

- Skills stay general and reusable; repo knowledge stays in the repo.
- Memory doesn't grow forever: lessons graduate into skills.
- Needs a way to link a skill to its process anchor, so using the skill
  brings in its memories.
- Editing a shared, cross-repo skill from one repo's lessons needs care:
  a lesson may be true only for this repo.
