# kyle-skills

Skills and plugins for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Lethe — Surgical Context Compaction

In Greek mythology, souls drank from the River Lethe to erase all memory before reincarnation. Claude Code's built-in `/compact` takes this same approach — a deep, destructive drink that wipes the conversation and replaces it with a best-effort summary.

**Lethe** is a measured *sip*. Instead of a blunt memory wipe, it classifies every segment of a conversation — session headers, architectural decisions, tool outputs, thinking blocks, error chains — and makes targeted per-segment decisions: keep, summarize, or drop. The session header, final working state, user preferences, and prior compaction boundaries are always preserved. Thinking blocks and progress markers are always removed. Everything else is either aggressively trimmed to 1-2 sentence summaries, moderately summarized, or evaluated by Claude for semantic importance.

The result is a 40-75% context reduction that preserves what matters — compared to `/compact`'s 90%+ reduction that often loses critical architectural context and accumulated decisions.

**How it works:**

```
Python (Analyze) → Claude (Decide) → Python (Splice) → Gate
```

Python scripts handle the structural work — parsing JSONL, walking the `parentUuid` chain, classifying entries, grouping them into typed segments. Claude handles the semantic work — reading the manifest, applying classification rules, evaluating ambiguous segments, writing targeted summaries. Python then re-synthesizes the conversation from the cut-plan, rewires the chain, and verifies integrity. A final gate checks the result against a configurable token threshold before promoting the working copy.

The original session data is never modified until all three conditions are met: successful splice, gate pass, and backup creation. If anything fails, the original is untouched.

Lethe auto-detects your terminal emulator, launches compactor sessions in new windows, and handles the full lifecycle — kill, compact, relaunch — automatically. Safe to run repeatedly. No dependencies beyond Python 3.10+.

[`kyle-skills/lethe-claude`](https://github.com/kyle-skills/lethe-claude)

## License

MIT
