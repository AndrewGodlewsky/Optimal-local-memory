# Future work

Ideas noted for later. **Not in scope now**; don't design or build these
until the project owner says so.

## Memory audit process (user, 2026-10-20)

A periodic audit of memory, to:
- remove memories that are irrelevant or no longer true,
- assess whether memories have actually been relevant and useful to agents
  (for example, by tracking which memories were retrieved and whether the
  tasks that used them succeeded).

This complements [decision 0009](decisions/0009-memory-map-instead-of-line-review.md),
which trusts summarization at PR time instead of reviewing each memory.
