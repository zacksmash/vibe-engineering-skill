# Vibe Engineering

A [Claude Code](https://claude.com/claude-code) skill that turns feature building into a guided, step-through workflow: Claude plans with you, executes **one atomic change at a time**, lets work accumulate uncommitted, and hands you four concrete next-step suggestions after every step. You stay in control — Claude proposes, you decide, you commit when ready.

**Micro decisions where they matter; macro, let it flow.**

## Why

Coding agents tend toward two extremes: they either ask permission for everything or disappear for ten minutes and return with a 40-file diff you can't review. Vibe Engineering sits in the middle. Every change is small enough to review at a glance, nothing is ever committed automatically, and the interactive picker keeps the next move one keypress away — while still letting you open the throttle (Flow or Agent mode) when you trust the direction.

## How a session works

1. **You describe a feature** — `/vibe-engineer Build a teams feature with invitations`.
2. **The Grilling.** Claude enters Plan Mode, reads your codebase first, then interviews you in rounds until every design decision is resolved — with a recommended answer for every question. Say "just plan it" to skip ahead; unanswered questions become stated assumptions.
3. **Plan + Goal.** You get a short prose technical brief (not a checklist) plus a ready-to-run `/goal` command that defines "done" for the session. Claude records a safe-undo baseline and creates a `feat/*` branch.
4. **The loop.** Claude does one atomic step, reports exactly which files changed, and presents 4 suggestions in the picker — ordered by dependency, always advancing the code, evolving from scaffolding toward tests/validation/polish as the feature matures. Type a number to pick one, type your own instruction, or just ask a question (questions are free — no files touched).
5. **You commit.** Work accumulates uncommitted until you type `approve` or `commit`. Commits are scoped to the session's own files only — pre-existing dirty files and unrelated work are never swept up.
6. **Adversarial review.** When the diff gets substantial, the first suggestion becomes a parallel multi-agent review — 2–4 reviewer lenses chosen to fit the actual diff (security, performance, framework conventions, correctness, …), each run in isolation with no access to the coding session's reasoning, so they can't rationalize its decisions. Findings become the next round of work.
7. **Finish.** `finish` / `ship it` checks the Goal honestly, pushes the branch, and offers a PR.

### Developer commands

| You type | What happens |
| --- | --- |
| `1`–`4` or a suggestion | Claude does that step, no permission asked |
| `approve` / `commit` | Stage and commit the session's files (`vibe: <summary>`) |
| `reject` / `undo all` | Restore session files to the baseline (never `git clean`, never tree-wide) |
| `undo <task>` / `remove <file>` | Revert just that piece |
| `do 1, 2 and 3` | **Flow Mode** — batch several steps, then back to step-by-step |
| `just keep going` | **Agent Mode** — autonomous chunks with picker checkpoints (only you can enable it) |
| `pause` / `resume` | Step out of / back into the workflow, context intact |
| `finish` / `ship it` | Verify the Goal, push, offer a PR |

Sessions adapt to the job — new feature, bug fix, refactor, dependency upgrade, performance work, UI, security hardening, docs — see [session-types.md](src/references/session-types.md).

**Bug-fix sessions fill the same loop differently.** The grilling gets short and symptom-focused — what broke, what you expected, when it started, what changed — and the steps become the six diagnosis phases: build a command that goes red on *this* bug before touching any code, minimise the repro, rank falsifiable hypotheses in the picker, instrument, then fix behind a regression test. Same loop, same picker, different work. See [bug-diagnosis.md](src/references/bug-diagnosis.md).

## Installation

Copy the contents of [src/](src/) into a `vibe-engineer` skill folder:

```bash
# Personal (all projects)
mkdir -p ~/.claude/skills/vibe-engineer
cp -R src/. ~/.claude/skills/vibe-engineer/

# Or per-project
mkdir -p .claude/skills/vibe-engineer
cp -R src/. .claude/skills/vibe-engineer/
```

You should end up with `SKILL.md` directly inside the `vibe-engineer` folder. The `src/.` is deliberate — `cp -R src/ <dir>/` copies the folder *into* an existing target, leaving you with `vibe-engineer/src/SKILL.md`, which Claude Code won't load.

Restart Claude Code (or start a new session) and the skill is available.

## Usage

Invoke it directly:

```
/vibe-engineer Build a teams feature with invitations
```

Or just say what you want naturally — "vibe", "start session", "plan this feature", "let's step through building X" all trigger it. It deliberately does **not** trigger for quick one-off fixes or simple questions; those don't need the ceremony.

### Optional: simplified English

Off by default. Ask for **ASD-STE100 Simplified Technical English** ("use STE", "simplified English") at any point and Claude writes its prose — explanations, plans, suggestions, picker labels — in it for the rest of the session. Code, paths, commands, and quoted output are untouched. Say so again to turn it off.

## Requirements

Built for **Claude Code specifically** — it depends on features other harnesses don't have:

- **`AskUserQuestion` picker** — the interactive 4-option selector is the workflow's primary navigation, not a nicety. (If your environment renders it as plain text, typing a number still works.)
- **Plan Mode** — used for the grilling and planning phase.
- **`/goal`** (Claude Code v2.1.139+) — optional but recommended; keeps the session honest about "done".
- **`Workflow` tool** (v2.1.154+, paid plans) — powers the parallel adversarial review, with an automatic fallback to `Agent`-tool subagents when unavailable.

## Repository layout

```
src/
├── SKILL.md                        # The skill itself — rules, session flow, core loop
└── references/                     # Loaded on demand to keep the main skill lean
    ├── modes.md                    # Step / Flow / Agent / Ask modes, pausing
    ├── session-types.md            # Per-session-type planning strategies
    ├── bug-diagnosis.md            # The six-phase diagnosis loop for bug-fix sessions
    ├── adversarial-review.md       # Reviewer isolation, lens selection, workflow script
    └── troubleshooting.md          # Common issues and version requirements
```

## License

MIT
