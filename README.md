# Vibe Engineering

A [Claude Code](https://claude.com/claude-code) skill that turns feature building into a guided, step-through workflow: Claude plans with you, executes **one atomic change at a time**, lets work accumulate uncommitted, and hands you four concrete next-step suggestions after every step. You stay in control — Claude proposes, you decide, you commit when ready.

**Micro decisions where they matter; macro, let it flow.**

## Why

Coding agents tend toward two extremes: they either ask permission for everything or disappear for ten minutes and return with a 40-file diff you can't review. Vibe Engineering sits in the middle. Every change is small enough to review at a glance, nothing is ever committed automatically, and the interactive picker keeps the next move one keypress away — while still letting you open the throttle (Flow or Agent mode) when you trust the direction.

## How a session works

1. **You describe a feature** — `/vibe-engineer Build a teams feature with invitations`.
2. **The Grilling.** Claude enters Plan Mode, reads your codebase first, then asks informed questions in rounds until the blocking and hard-to-reverse decisions are settled. Say "just plan it" to skip ahead; unanswered important questions become stated assumptions.
3. **Plan + optional Goal.** You get a short prose technical brief and an exact definition of done. After plan approval and safe workspace setup, Claude offers the matching `/goal` command only when the current mode and picker-timeout setting are compatible.
4. **Safe workspace.** After approval, Claude records the Git baselines and creates a task branch for a clean tree. For a dirty tree, it prefers a dedicated worktree, verifies the worktree's exact base commit, and never stashes without asking.
5. **The loop.** Claude completes one coherent, reviewable outcome, validates it, reports every changed file, and presents 4 dependency-ordered suggestions in the picker. Type a number, give your own instruction, or ask a question (questions are free — no files touched).
6. **You commit.** Work accumulates uncommitted until you type `approve` or `commit`. Path-limited commits exclude unrelated changes even when those changes were already staged.
7. **Adversarial review.** When the complete session diff gets substantial, the first suggestion becomes a parallel multi-agent review. Claude chooses 2–4 lenses for the actual risk, includes untracked files, gives reviewers no coding rationale, and rejects the run if a reviewer changes the workspace.
8. **Finish.** `finish` / `ship it` checks the completion condition honestly, pushes the branch, and offers a PR.

### Developer commands

| You type | What happens |
| --- | --- |
| `1`–`4` or a suggestion | Claude does that step without a redundant confirmation |
| `approve` / `commit` | Stage and commit the session's files (`vibe: <summary>`) |
| `reject` / `undo all` | Restore only the current uncommitted batch to its batch baseline |
| `undo <task>` / `remove <file>` | Reverse that task's recorded patch without erasing later work |
| `do 1, 2 and 3` | **Flow Mode** — batch several steps, then back to step-by-step |
| `just keep going` | **Agent Mode** — autonomous chunks with picker checkpoints (only you can enable it) |
| `pause` / `resume` | Step out of / back into the workflow, context intact |
| `finish` / `ship it` | Verify the completion condition, push, offer a PR |

Sessions adapt to the job — new feature, bug fix, refactor, dependency upgrade, performance work, UI, security hardening, docs — see [session-types.md](src/references/session-types.md).

**Bug-fix sessions use the same loop with diagnostic steps.** The grilling is short and symptom-focused, then Claude builds a red-capable feedback loop, minimizes the reproduction, ranks falsifiable hypotheses in the picker, instruments, and fixes behind a regression test. Same atomic checkpoints, different work; see [bug-diagnosis.md](src/references/bug-diagnosis.md).

## Installation

Copy the contents of [src/](src/) into a `vibe-engineer` skill folder:

```bash
# Personal (all projects)
mkdir -p ~/.claude/skills/vibe-engineer
cp src/SKILL.md ~/.claude/skills/vibe-engineer/
cp -R src/references ~/.claude/skills/vibe-engineer/

# Or per-project
mkdir -p .claude/skills/vibe-engineer
cp src/SKILL.md .claude/skills/vibe-engineer/
cp -R src/references .claude/skills/vibe-engineer/
```

Claude Code detects `SKILL.md` changes inside known skill directories live. Restart only if the top-level skills directory did not exist when the current session started.

## Usage

Invoke it directly:

```
/vibe-engineer Build a teams feature with invitations
```

Or just say what you want naturally — "vibe", "start session", "plan this feature", "let's step through building X" all trigger it. It deliberately does **not** trigger for quick one-off fixes or simple questions; those don't need the ceremony.

### Optional: simplified English

Ordinary English is the default. Ask for **ASD-STE100 Simplified Technical English** ("use STE" or "simplified English") and Claude applies it to prose, plans, suggestions, and picker labels for the rest of the session. Code, paths, commands, and quoted output stay unchanged. Ask to turn it off at any time.

## Requirements

Built for **Claude Code specifically**. The interaction requires the first two capabilities below. The others are optional integrations. Full branch, commit, and undo safety also requires a Git repository with at least one commit.

- **[`AskUserQuestion` picker](https://code.claude.com/docs/en/tools-reference#askuserquestion-tool-behavior)** — the interactive 4-option selector is the workflow's primary navigation, not a nicety. (If your environment renders it as plain text, typing a number still works.)
- **[Plan Mode](https://code.claude.com/docs/en/permission-modes)** — used for the grilling and planning phase.
- **[`/goal`](https://code.claude.com/docs/en/goal)** (Claude Code v2.1.139+) — optional; adds a transcript-based completion evaluator. It is best suited to Agent Mode. In Step or Flow Mode, use it only when the `AskUserQuestion` auto-continue timeout is disabled so an unattended timeout cannot undermine the human checkpoint.
- **[Dynamic workflows](https://code.claude.com/docs/en/workflows)** (v2.1.154+, supported paid or API/provider setup) — preferred for parallel adversarial review, with a fallback to fresh `Agent`-tool subagents.

Without Git or an initial commit, the step-through loop can still run, but safe branching, scoped commits, and baseline restoration are unavailable. The skill must disclose that limitation before editing.

## Repository layout

```
src/
├── SKILL.md                        # The skill itself — rules, session flow, core loop
└── references/                     # Loaded on demand to keep the main skill lean
    ├── modes.md                    # Step / Flow / Agent / Ask modes, pausing
    ├── session-types.md            # Per-session-type planning strategies
    ├── session-safety.md           # Branch, dirty-tree, commit, and undo safety
    ├── bug-diagnosis.md            # Repro-first loop for bug-fix sessions
    ├── adversarial-review.md       # Reviewer isolation, lens selection, workflow script
    └── troubleshooting.md          # Common issues and version requirements
```

## License

[MIT](LICENSE)
