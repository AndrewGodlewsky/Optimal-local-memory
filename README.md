# Optimal-local-memory

A local, per-project memory for AI coding agents, built as a skill.

## Inspiration

This project is based on Victor Taelin's design note
**[OptChat / UniiChat: one chat that never ends](https://gist.github.com/VictorTaelin/91837951a5ce5b38f341ec1ba1df6449)**.

The core idea is that **the chat itself is the memory**:

- Every message is logged word for word to an append-only log, forever.
- A cheap model compresses the log into a binary tree of one-line
  (≤512 byte) summaries: each message becomes a line, and adjacent lines
  are merged in pairs, again and again.
- Each turn the agent sees a fixed-size **view** of that tree: recent
  history in fine detail, older history in coarse summaries.
- When a summary is too vague, the agent **zooms** into it, down to the
  original message.

The result is a context of constant size where nothing is ever lost.
