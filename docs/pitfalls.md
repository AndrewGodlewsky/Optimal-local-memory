# Pitfalls

Problems to design for before building. Mitigations are candidates
(**Proposed, agent call**) unless marked otherwise.

| Pitfall | Why it hurts | Candidate mitigations |
|---|---|---|
| **Agents don't call memory** | The system is invisible; nothing improves | Firm rules in instruction files; cheap `memory_start`; good tool descriptions; skills tied to processes; hooks where available; measure with an eval set |
| **Too much context** | Agent loses focus; costs tokens | Size budgets per layer and per memory; ranked, filtered search; one-line results first |
| **Too little context** | Agent repeats mistakes | Anchors filled in automatically from touched files; eval set measuring recall |
| **Merge conflicts** | Many writers on many branches | One file per memory; ULIDs; no shared, hand-edited index; derived data never committed |
| **Stale memories** | Memory says X; code says Y | "Code wins" rule; supersede and retire; detect anchors pointing to missing paths; show memory age |
| **Noise and duplicates** | Memory fills with low-value or repeated entries | Drafting prompt with clear "don't draft" rules; PR review; duplicate check at draft time; lessons graduate into skills |
| **Wrong or overstated memories** | Agents act on false history | Evidence links; "never overstate progress" in the drafting prompt; review |
| **Memory as prompt injection** | Text in memory is read by agents | Treat memory as data, never instructions, in tool output framing; PR review (accepted, user) |
| **Secrets in evidence** | Transcripts can contain tokens and env vars | Secret scanning before commit; clip tool output; decide carefully what evidence is committed |
| **Repo bloat** | Transcripts are large | Commit summaries rather than raw logs; size limits; possibly a separate branch or Git LFS for evidence |
| **Unreviewed memories on branches** | Agents on a branch see drafts nobody checked | Mark draft status in results; consider lower ranking until merged |
| **Abandoned branches** | Their memories should vanish | Handled by git: unmerged memories never reach `main` (accepted, user) |
| **Process name drift** | Same process under several names | A shared process list; validation at draft time |
| **Uneven harnesses** | Copilot captures less than Claude Code | Design the core around tools every harness has; treat hooks as a bonus |
| **Lessons wrongly pushed into shared skills** | A repo-specific lesson edited into a cross-repo skill | Proposals only, never automatic edits; flag "repo-only" lessons |
| **Search misses** | Keyword search misses paraphrases | Anchors first; measure; add embeddings only if the evals show the need |
