# Vibe Engineering — Troubleshooting

## Skill doesn't trigger

If "start session" or "plan this feature" doesn't load the skill: invoke it directly with `/vibe-engineer`, and check that the skill is enabled in settings.

## UI picker appears as plain text

Some environments render the picker suggestions as plain text instead of the interactive selector. The developer can still type a number ("2") to select. The skill emphasizes the picker in several places to maximize consistency.

## Steps too large

Usually means the instruction was too broad. Break it down — pick a specific first step from the suggestions rather than "build the auth system." The skill keeps steps atomic, but explicit, narrow instructions from the developer always help.

## Goal not working

`/goal` requires Claude Code v2.1.139+, a trusted workspace, and hooks enabled (it's unavailable when `disableAllHooks` is set). Only the developer can run it — you hand them the command in the plan but cannot execute it. It can be set at any point in the session.

## `/goal` pushes past a picker in Step Mode

Step Mode ends every turn at the picker and waits. An active `/goal` restarts the agent when a turn ends before the goal is met, so in principle it can re-prompt at a picker pause and carry the agent into the next step without the developer choosing — which would break the skill's primary promise.

**This has not been reproduced in a live Step Mode session; the rule is defensive.** SKILL.md (Modes) tells the agent to treat a picker pause as a legitimate turn end in every mode: on a `/goal` re-prompt it re-presents the picker instead of running ahead. If you do see the agent skip a picker while a goal is active, say "stop" or "pause" — then either re-pick from the refreshed picker or run `/goal clear` and set a narrower goal. Report it, because it means the rule isn't holding.

## Adversarial review doesn't use a workflow

The `Workflow` tool requires Claude Code v2.1.154+ and a paid plan. If it's unavailable, fall back to parallel reviewer subagents via the `Agent` tool — same panel, same isolation. See `references/adversarial-review.md`.

## Suggestions not appearing

If a response lacks the 4 suggestions during the active loop, type "suggestions" or "what should I do next" to prompt the picker. This should be rare — the skill repeats the instruction in several places.
