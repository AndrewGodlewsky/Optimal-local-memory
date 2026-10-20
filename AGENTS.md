# AGENTS.md

Instructions for any AI agent (GitHub Copilot, Claude Code, or other)
working in this repository.

## What this project is

A repo-local memory system for AI coding agents, shared by a team through
git. **Read [CONTEXT.md](CONTEXT.md) before doing anything else.** It holds
the objectives, requirements, decisions and open questions.

## Current phase: design

- Do **not** write product code, scaffold the project, or install
  dependencies until the design document in `docs/superpowers/specs/` has
  been approved by the project owner.
- Writing and editing documentation and planning files is fine.

## Rules

- **Keep CONTEXT.md current.** When a decision is made or changed, update
  CONTEXT.md in the same change.
- **Mark who decided.** Label decisions **(user)** or **(agent call)**.
  Never present your own proposal as something the user agreed to.
- **Stay harness-agnostic.** The core must work in GitHub Copilot (VS Code
  and other Copilot harnesses) and Claude Code. Harness-specific features
  belong in thin adapters only.
- **Planned stack:** TypeScript/Node MCP server, run with `npx`.

## Where things live

- `README.md`: short project overview and the link to the inspiring gist.
- `CONTEXT.md`: objectives, requirements, decisions, pitfalls, open
  questions.
- `docs/README.md`: index of the deeper documentation.
- `docs/decisions/`: one record per decision (ADRs). Add a new record for
  each new decision; supersede rather than rewrite accepted ones, and add
  it to the table in `docs/README.md`.
- `docs/architecture/`: overview, storage, retrieval and capture designs.
- `docs/pitfalls.md`: risks and candidate mitigations.
- `docs/background/`: the OptChat gist this project builds on, explained.
- `docs/superpowers/specs/`: design documents (not yet written).
