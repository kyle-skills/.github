# kyle-skills

Skills and plugins for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — extending autonomous coding sessions with surgical memory management and multi-agent orchestration.

## Projects

### Lethe — Surgical Context Compaction

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

---

### The Elevated Stage — Multi-Agent Orchestration

[**The Elevated Stage**](https://github.com/The-Elevated-Stage) is a companion organization housing a full orchestration suite for autonomous multi-session development with Claude Code. It applies the structure of a symphony orchestra to software implementation — a Conductor coordinates, Musicians execute in isolated sessions, and specialized roles handle everything from research-driven design to fault recovery.

```
Dramaturg → Arranger → Conductor → Musicians
(design)    (plan)     (orchestrate) (implement)
```

The pipeline begins with the **Dramaturg**, which explores and validates design decisions through external research before committing to an approach. The **Arranger** converts validated designs into phased implementation plans with machine-readable section boundaries. The **Conductor** — operating on a 1M-token context budget dedicated entirely to coordination — dispatches **Copyists** to extract self-contained task instructions and **Musicians** to execute them in dedicated terminal sessions.

When problems arise, the Conductor has five correction attempts before escalating to the **Repetiteur**, an autonomous re-planner that restructures the remaining work around blockers. The **Souffleur** watches from the wings as an external liveness monitor — if the Conductor's heartbeat goes silent, it recovers orchestration state from the database and relaunches the performance.

All state lives in a shared SQLite database, not session context. Musicians claim tasks atomically. Every state transition updates a heartbeat. The system is designed to run unattended and recover from failures without user intervention.

**The ensemble:**

| Role | Purpose | Repo |
|---|---|---|
| Conductor | Coordination layer — launches sessions, reviews output, handles error recovery | [`conductor`](https://github.com/The-Elevated-Stage/conductor) |
| Musician | Implementation agent — executes task instructions in isolated terminal sessions | [`musician`](https://github.com/The-Elevated-Stage/musician) |
| Copyist | Task extraction — generates self-contained instruction files from plan phases | [`copyist`](https://github.com/The-Elevated-Stage/copyist) |
| Dramaturg | Design exploration — research-validated design documents and decision journals | [`dramaturg`](https://github.com/The-Elevated-Stage/dramaturg) |
| Repetiteur | Re-planning — autonomous mid-implementation revision when corrections fail | [`repetiteur`](https://github.com/The-Elevated-Stage/repetiteur) |
| Souffleur | Watchdog — monitors Conductor liveness and performs crash recovery | [`souffleur`](https://github.com/The-Elevated-Stage/souffleur) |
| Repertoire | Shared contracts — priority chains, verification rules, journal conventions | [`repertoire`](https://github.com/The-Elevated-Stage/repertoire) |
| Stagecraft | Meta-documentation — architecture decisions, expansion planning, design specs | [`stagecraft`](https://github.com/The-Elevated-Stage/stagecraft) |

---

## How They Connect

Lethe was built independently but integrates directly into the orchestration pipeline. When a Conductor or Musician session exhausts its context budget, the Souffleur can route recovery through Lethe for surgical compaction — preserving the accumulated architectural decisions and task state that a blunt `/compact` would dissolve. In standalone use, Lethe works with any Claude Code session regardless of whether orchestration is involved.

## License

MIT
