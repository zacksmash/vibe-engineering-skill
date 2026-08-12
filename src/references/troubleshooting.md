# Vibe Engineering — Troubleshooting

## Skill doesn't trigger

If "start session" or "plan this feature" doesn't load the skill: invoke it directly with `/vibe-engineer`, and check that the skill is enabled in settings.

## UI picker appears as plain text

Some Claude Code surfaces can render the `AskUserQuestion` result as plain text. The developer can still type a number ("2") to select. Continue to call `AskUserQuestion`; do not voluntarily replace it with a Markdown numbered menu.

## Steps too large

Usually means the instruction was too broad. Break it down — pick a specific first step from the suggestions rather than "build the auth system." The skill keeps steps atomic, but explicit, narrow instructions from the developer always help.

## Goal not working

`/goal` requires Claude Code v2.1.139+, a trusted workspace, and hooks enabled. It is unavailable when `disableAllHooks` is set or managed settings enable `allowManagedHooksOnly`. Only the developer can run it. Setting it starts a turn immediately; the evaluator then keeps starting turns until the transcript proves the condition, and clears automatically when it succeeds. Claude must still wait at every vibe picker checkpoint. Do not provide the runnable command in Step/Flow Mode while `askUserQuestionTimeout` is enabled; offer Agent Mode, disabling the timeout, or skipping the goal. The evaluator does not inspect files or run commands, so Claude must run and surface every proof named in the condition.

## Adversarial review doesn't use a workflow

The `Workflow` tool requires Claude Code v2.1.154+ and a supported paid or API/provider setup. On Pro, it must also be enabled in `/config`; organization policy can disable it. If unavailable, fall back to fresh parallel reviewer subagents through the `Agent` tool. See `references/adversarial-review.md`.

## Safe branch setup is blocked

The full Git workflow requires a repository with at least one commit. For a dirty tree, prefer a dedicated worktree, but verify its exact base: Claude Code can default new worktrees to the remote default branch rather than local `HEAD`. Worktrees also omit uncommitted files unless `.worktreeinclude` copies selected ignored files. If a worktree is unavailable, ask before stashing and include untracked files. See `references/session-safety.md`; never claim safe undo when no reliable baseline exists.

## A reviewer changed files

Workflow agents run in `acceptEdits`, even for a review. Stop, compare the repository with the pre-review snapshot, and discard the run's findings. Reverse only a provably reviewer-owned patch; never restore a whole file over legitimate work. Then validate and rerun the review. See `references/adversarial-review.md`.

## Suggestions not appearing

If a response lacks the 4 suggestions during the active loop, type "suggestions" or "what should I do next" to prompt the picker. This should be rare — the skill repeats the instruction in several places.
