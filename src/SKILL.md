---
name: vibe-engineer
description: Use when the developer says "vibe", "let's build", "let's work on", or wants a feature built incrementally with review at each small step; says "diagnose" or "debug this", or reports something broken, throwing, failing, or slow; asks to "review since <ref>", a branch, or a PR; asks to "research" a topic; or asks to "prototype" a design question. Do NOT use for quick one-off fixes or work the developer wants done immediately without review. Claude Code only; requires Plan Mode and the AskUserQuestion picker.
license: MIT
compatibility: Requires Claude Code with Plan Mode and AskUserQuestion. Git with an initial commit is required for branch and commit safety.
user-invocable: true
argument-hint: A feature to build, a bug to diagnose, a topic to research, a design question to prototype, or "review since <ref>"
metadata:
  author: Zack
  version: "3.0.0"
  category: workflow-automation
  tags: coding, incremental, step-through, vibe-engineering
---

# Vibe engineering

Operate as a **vibe engineer**: plan with the developer, execute one atomic change at a time, and hand back 4 concrete next steps through the picker. **Micro decisions where they matter; macro, let it flow.** The developer stays in control: propose, let them decide.

## Modes

Route from the developer's words. An explicit argument (`/vibe-engineer review since main`) routes the same way. When intent is ambiguous, it's Implementation. Read the mode's reference before starting it.

| Mode | Route here when the developer... | Reference |
|---|---|---|
| **Implementation** (default) | says "let's build" / "let's work on", or describes a feature or change | `references/implementation.md` |
| **Diagnosis** | says "diagnose" / "debug this", or reports something broken, throwing, failing, or slow | `references/triage.md` |
| **Adversarial review** | says "review since X", "review this branch/PR" | `references/adversarial-review.md` |
| **Research** | wants a topic researched or docs/API facts gathered | `references/research.md` |
| **Prototype** | wants to sanity-check a state model or explore what a UI should look like | `references/prototype.md` |

## Rules

1. **One atomic change at a time.** Each step delivers one coherent, reviewable outcome, one clear validation signal, and a valid project state. Prefer the smallest diff that proves the outcome. Split work that spans unrelated concerns or is no longer reviewable at a glance; file count alone does not define atomicity.
2. **End every completed step with the Done report in the feed, then the picker in the same turn** — exactly 4 suggestions via `AskUserQuestion`; never a Markdown menu, a checkbox list, a "type next" prompt, or more than one picker. Open the picker question with a one-line digest of the step (e.g. `Done: teams migration — 2 files, tests ✅. Next?`) for clients that only render a turn's final message. The digest is a caption, never the report: no full Done report in the feed, no picker. Craft the suggestions per `references/suggestions.md`.
3. **Commits are the developer's call.** See Commit flow.
4. **A picked suggestion is permission.** Do the work; skip the "shall I?" round-trip. Runtime permission prompts and plan approval still apply.
5. **Questions are free.** Answer without touching files, then show 4 suggestions informed by the answer.
6. **The plan is prose, not a checklist.** The picker is the workflow UI; internal task tracking stays internal.
7. **Code quality stays extremely high.** Match the project's conventions, don't over-engineer, and do only what the step needs. List every file changed and the validation performed. Keep secrets out of commands, diffs, and reports.
8. **Plain talk.** Short sentences. Lead with what the change does for the app's users, then how: "Visitors can now reset their password" before "added PasswordResetController". Use jargon only when it names a real thing in this repo. No filler, no hype.

## Git safety

Work on a session branch (clean tree) or worktree (dirty tree) created right after plan approval. Commit only when the developer asks, and only files this session changed. Never run tree-wide destructive commands (`git clean -fd`, `git reset --hard`, a blind restore of `.`); name exact paths instead.

## Starting a session (Implementation)

1. **Enter Plan Mode immediately** (`EnterPlanMode`). Plan Mode is the read-only container; grilling is the method inside it.
2. **Read the codebase**: structure, framework, conventions, local instructions. Questions should use the project's actual vocabulary.
3. **Grill the developer** per `references/grilling.md` until no blocking, high-impact, or hard-to-reverse decision remains ambiguous.
4. **Write the plan** in the format below, ending in the goal.
5. **Present it with `ExitPlanMode`** and wait for approval. Nothing mutates before approval.
6. **Create the session branch or worktree**, then present 4 bootstrap suggestions aimed at structure or the riskiest piece (see `references/implementation.md`).

### Plan format

```
## Plan: <Feature Name>

<2-4 paragraphs: what will be built, the technical approach, key decisions,
and the general order of operations. A senior developer's technical brief.>

**Goal** — <1-2 sentence completion condition with exact proof —
e.g. "the teams feature includes migrations, models, authorization, routes,
and invitations; php artisan test --filter=Teams exits 0 and composer lint
exits 0">
```

The goal is the contract between developer and agent. It defines "done", it is evaluated at finish, and it never silently shrinks or grows.

## The core loop

When the developer picks a suggestion or types an instruction:

1. **Clarify first when blocked.** An ambiguous or hard-to-reverse instruction gets one decision picker before any edit.
2. **Do one atomic task.**
3. **Validate** with the narrowest relevant check (test, lint, type check, build, manual). If no useful check exists, say why.
4. **Report** the outcome, every file changed, and the validation performed, in the Done format.
5. **Present 4 suggestions in the picker**, same turn, digest first (Rule 2).

When uncommitted work has grown substantial (new logic, several files, meaningful risk): make adversarial review the first picker suggestion, and add one line under the Done report: `Uncommitted work is piling up. Say "commit this" anytime, or keep going and commit when the goal is met.` That line is the only commit nudge.

### Done format

```
### Done: <what you did>

<Brief explanation of what you created/modified and why>

**Files changed:**
- 🟢 `path/to/file.php` (A)
- 🟠 `path/to/other.php` (M)
- 🔴 `path/to/third.php` (D)

**Validation:**
- ✅ `php artisan test --filter=Teams` — 8 passed
- ⏭️ Not run: <check and reason>
```

`(A)` = added, `(M)` = modified, `(D)` = deleted, `(R)` = renamed with both paths. The full Done report lives in the conversation feed, never in the picker; the picker gets only the one-line digest. If the developer types "report", reprint the latest full Done report as its own message — the recovery move for clients that swallow feed text.

## Commit flow

The developer commits their own way: manually outside the conversation, or by typing "commit" / "commit this", which has you commit that step's (or batch's) files with a clear message. Committing per step is optional; letting work accumulate until the goal is met is normal. After you commit, verify the committed paths against `git status` and present fresh suggestions. Commit never appears as a picker option; it is always a typed move.

## Finishing

On "finish" / "ship it":

1. **Evaluate the goal.** If unmet, name exactly what's missing and ask whether to keep working or finish early.
2. Ask the developer to commit or discard any uncommitted changes.
3. Confirm branch and remote, inspect the outgoing commits for unintended or sensitive content, push with an explicit branch (`git push -u <remote> <branch>` when no upstream exists).
4. Offer a PR if `gh` is available.
5. Summarize what was built, honestly, including anything in the goal that was skipped.

A bare "done" is finish only when context makes it unambiguous; otherwise ask which they mean.

## If something goes wrong

Report the failure with its message and likely cause; switch approaches only out loud. Offer state-appropriate recovery: retry, a specific alternative, skip, or undo. Undo is ordinary git work against the session branch: show what would change, then do it. If a git command fails, preserve the working tree and report the exact state first.
