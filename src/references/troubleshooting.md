# Vibe Engineering — Troubleshooting

## Skill doesn't trigger

If "start session" or "plan this feature" doesn't load the skill: invoke it directly with `/vibe-engineer`, and check that the skill is enabled in settings.

## UI picker appears as plain text

Some environments render the picker suggestions as plain text instead of the interactive selector. The developer can still type a number ("2") to select. The skill emphasizes the picker in several places to maximize consistency.

## Steps too large

Usually means the instruction was too broad. Break it down — pick a specific first step from the suggestions rather than "build the auth system." The skill keeps steps atomic, but explicit, narrow instructions from the developer always help.

## Goal not working

`/goal` requires Claude Code v2.1.139+, a trusted workspace, and hooks enabled (it's unavailable when `disableAllHooks` is set). Only the developer can run it — you hand them the command in the plan but cannot execute it. It can be set at any point in the session.

## Does `/goal` push the agent past a picker in Step Mode?

No — they compose cleanly. Confirmed in normal use: an active goal does not carry the agent through a picker pause, and no rule is needed to prevent it.

The reason appears to be that a picker isn't a turn end. `AskUserQuestion` is a tool call, so the turn is blocked awaiting its result rather than finished, and the Stop hook `/goal` runs on doesn't fire there. The evaluator only restarts the agent on a genuine premature stop — a turn that ends with no picker and nothing pending — which is exactly the case it should catch, and the same event Agent Mode's `/goal` pairing relies on (see `modes.md`).

If you ever do see the agent skip a picker with a goal active, that's worth reporting. Say "stop", then re-pick or run `/goal clear`.

## Adversarial review doesn't use a workflow

The `Workflow` tool requires Claude Code v2.1.154+ and a paid plan. If it's unavailable, fall back to parallel reviewer subagents via the `Agent` tool — same panel, same isolation. See `references/adversarial-review.md`.

## The adversarial review asks for approval before it runs

Expected on first use — the dynamic-workflows process prompts once per project. Choose "don't ask again for this workflow in this project" to suppress it on later runs. See `references/adversarial-review.md`.

## Suggestions not appearing

If a response lacks the 4 suggestions during the active loop, type "suggestions" or "what should I do next" to prompt the picker. This should be rare — the skill repeats the instruction in several places. One case is legitimate: an open-ended clarification question is allowed to take the turn's only picker (SKILL.md Rule 2, rung 3). Answer it and the suggestions come back next turn.
