# Harness and GitHub capabilities (research, 2026-10-20)

Web research by an agent, from official docs where possible. Confidence is
marked **high / medium / low**. Re-check before relying on anything marked
preview or low; these products change quickly.

## Session transcripts

| Harness | How to get session content | Format stability |
|---|---|---|
| VS Code Copilot | Hooks receive `transcript_path`; "Chat: Export Chat..." writes JSON; Agent Debug Logs export OTLP JSON (high). On-disk location undocumented (low) | "Not a stable hook API and might change" |
| Copilot CLI | `transcriptPath` on `agentStop`, `subagentStart/Stop`, `preCompact`; `postToolUse` has `toolArgs` and `toolResult`; `/share file` exports Markdown (high). Sessions stored in `~/.copilot/session-state/`, synced to the user's GitHub account by default (high) | Internal |
| Copilot coding agent | Hooks from `.github/hooks/*.json` only; VM is ephemeral, so a hook must commit/push anything to keep (high). Logs stay on GitHub.com (medium) | Internal |
| Claude Code | Every hook gets `transcript_path`; JSONL at `~/.claude/projects/<path>/<session>.jsonl`; deleted after `cleanupPeriodDays` (default 30) (high) | Internal; "changes between versions" |

**Implication:** build evidence from per-event hook fields (prompt, tool
name, args, result) into our own normalized log, rather than parsing each
harness's transcript file.

## Hooks

- **VS Code (preview):** SessionStart, UserPromptSubmit, PreToolUse,
  PostToolUse, PreCompact, SubagentStart, SubagentStop, Stop. **No
  SessionEnd.** Config `.github/hooks/*.json`, `~/.copilot/hooks/*.json`;
  can read `.claude/settings.json` hooks if `chat.useClaudeHooks` is on (high).
- **Copilot CLI:** sessionStart, sessionEnd, userPromptSubmitted,
  preToolUse, postToolUse, postToolUseFailure, agentStop, subagentStart,
  subagentStop, preCompact, errorOccurred and more. Config includes
  `.github/hooks/*.json` (high).
- **Copilot coding agent:** `.github/hooks/*.json` only; sessionStart and
  sessionEnd once per job (high).
- **Claude Code:** 33 events including SessionStart, SessionEnd,
  UserPromptSubmit, PostToolUse, Stop, PreCompact; input includes
  `session_id` and `transcript_path`; Stop provides `last_assistant_message`
  (high).

**Implication:** `.github/hooks/*.json` is one shared hook location for
all three Copilot surfaces; Claude Code needs its own config.

## Skills, MCP, instructions

- **Agent Skills:** supported across Copilot (VS Code, CLI, coding agent,
  code review, JetBrains, Visual Studio). Project dirs: `.github/skills`,
  `.claude/skills`, `.agents/skills` (high).
- **MCP:**
  - VS Code: `.mcp.json` at workspace root (experimental) or
    `.vscode/mcp.json` (high).
  - Copilot CLI: `.mcp.json`, `.github/mcp.json`; not `.vscode/mcp.json`
    (high).
  - Coding agent: configured in repo Settings, **tools only**, explicit
    `tools` allowlist, tools run without approval (high).
- **Instruction files:**
  - VS Code Copilot reads `AGENTS.md`, `copilot-instructions.md` and
    `*.instructions.md`, but **not** `CLAUDE.md` (high).
  - Claude Code reads `AGENTS.md` natively, but by default only when no
    `CLAUDE.md` exists, so our `CLAUDE.md` with `@AGENTS.md` is the right
    setup (high).

## GitHub limits

- **Repo size:** ideally under 1 GB, strongly advised under 5 GB (high).
- **Files:** Git warns above 50 MiB; GitHub blocks above 100 MiB (high).
- **Git LFS:** Free, Pro and free orgs get 10 GiB storage and 10 GiB/month
  bandwidth; Team and Enterprise Cloud get 250 GiB of each. Going over
  without a budget blocks pushes (high).

## Mermaid on GitHub

- Rendered in a sandboxed iframe (high).
- `click` links on nodes are blocked or unreliable; the workaround is
  Markdown links outside the diagram (medium, community sources).
- Pan, zoom and fullscreen controls: **unconfirmed**.

## Models for a background summarizer

- **GitHub Models was retired on 2026-07-30** (high).
- **Copilot CLI in GitHub Actions** works with `copilot -p ...`, using
  `GITHUB_TOKEN` (if the org allows) or a PAT with "Copilot Requests" (high).
  It is billed in GitHub AI Credits by token usage, since 2026-06-01
  (medium/high).
- Otherwise a separate model API key (e.g. Anthropic) is needed.
