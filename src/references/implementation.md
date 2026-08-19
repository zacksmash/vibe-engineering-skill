# Implementation

Detail for the build loop, plus the three throttles the developer can shift between.

## Working a task

- **Know the boundary before editing.** Note the before-state of every path the task may touch. Stop on external changes you can't explain rather than adopting them.
- **Reconcile after.** Check `git status` and command output when the task lands; formatters, generators, hooks, and subagents change files silently. The Done report's file list must match the real repository state, not memory.
- **Validate at the narrowest seam** that proves the outcome: one test filter beats the whole suite, a type check beats a full build.

## Bootstrap suggestions

The first picker after plan approval targets core structure or the riskiest piece, never finishing touches. Examples: landing page → layout shell / nav / hero; API integration → auth + connection setup; database feature → migrations + schema; UI component → base structure + props; refactor → characterization check, then the smallest high-risk seam.

## Throttles

Default is **Step**. Return to Step after any other throttle completes.

### Step (default)

One task at a time: execute, report, suggest.

### Flow

The developer batches: "do 1, 2, and 3", "handle all the migration and model stuff", "knock out the boilerplate". Run the batch sequentially, validate each task at its natural boundary, then one grouped Done report and the picker. Stop mid-batch only when validation fails or a blocking decision appears: finish the current task, ask one decision question, and resume the remainder after the answer. Take the sensible default on reversible micro-decisions and report it instead of interrupting.

### Agent

The developer hands over the plan: "just keep going", "run through the plan". Work in chunks of 2–4 related tasks; after each chunk, a Done report and the picker, so every chunk boundary is still a checkpoint. Only the developer activates Agent; stay in Step or Flow until they do. "Auto mode" alone is ambiguous (Claude Code uses Auto for a permission mode); ask which they mean.

## Pausing

"Pause" / "hold on" means step back; the developer is using the interface normally and context is preserved. Any later instruction ("resume", "continue", or just a task) drops back into the loop with a picker for where they left off.
