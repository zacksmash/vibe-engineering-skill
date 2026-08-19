# Adversarial review

Two entry points share one dispatch machine:

- **In-loop lens review** — offered from the core loop when uncommitted work is substantial (the moment is defined in SKILL.md): is this batch sound?
- **Standalone standards & spec review** — invoked directly ("review since main", "review this branch/PR"): does this change meet the contract?

## Dispatch (both entry points)

Reviewers run as parallel sub-agents spawned fresh, so they never see the coding session's reasoning and cannot rationalize a decision they never saw justified. Prefer the **`Workflow` tool**; when it's unavailable (it needs a recent Claude Code and can be disabled), say so and use fresh `Agent` subagents with the same prompts.

Each reviewer grounds itself in: the diff from the fixed point, the full content of untracked changed files (`git diff` omits them), project instructions (`CLAUDE.md`/`AGENTS.md`), and the code one dependency hop around each change. Each hunts what is *wrong* through its lens only — no praise, no summaries — and returns findings as severity, `file:line`, one sentence.

Reviewers are read-only; verify a clean `git status` after the run before trusting the results.

### Workflow script

Adapt the prompt details; keep the shape. Pass the project path, the fixed point, the changed-file list, and the chosen perspectives via `args`.

```js
export const meta = {
  name: 'vibe-adversarial-review',
  description: 'Adversarial review of session changes',
  phases: [{ title: 'Review' }],
}
const FINDINGS = {
  type: 'object', required: ['findings'], additionalProperties: false,
  properties: { findings: { type: 'array', items: {
    type: 'object', required: ['severity', 'location', 'issue'],
    additionalProperties: false,
    properties: { severity: { type: 'string', enum: ['critical', 'major', 'minor'] },
      location: { type: 'string' }, issue: { type: 'string' } } } } },
}
phase('Review')
const results = await parallel(args.perspectives.map(p => () =>
  agent(`Adversarial ${p.name} review of the changes in ${args.cwd}.
This is a read-only review; do not edit files or run state-changing commands.
The fixed point is ${args.baseline}. The changed files are:
${JSON.stringify(args.files)}
Diff tracked files against the fixed point and read untracked ones in full.
Ground yourself in CLAUDE.md/AGENTS.md if present and the code one dependency
hop around each change. Find what is WRONG through the ${p.name} lens only:
${p.focus}. No praise or summaries. Return findings with severity and file:line.`,
    { label: `review:${p.key}`, schema: FINDINGS })))
return args.perspectives.map((p, i) => ({
  perspective: p.name,
  status: results[i] ? 'completed' : 'failed',
  findings: results[i]?.findings || [],
}))
```

A null reviewer result is a failed perspective, not an empty review; report or rerun it.

## In-loop lens review

Pick the 2–4 lenses that will surface the most real problems in *this* diff — never a fixed panel. Read the signal:

- **What changed** — auth or input handling pulls in security; query-, loop-, or render-heavy code pulls in performance; a public API, shared module, or schema change pulls in interface/compatibility/data integrity.
- **Language & stack** — match the lens to the idioms in use: concurrency for Go or threaded code, type and null safety for TypeScript, framework conventions for the framework at hand.
- **Task stage** — early scaffolding favors architecture and conventions; a maturing feature favors correctness and edge cases; pre-ship favors security, performance, tests, accessibility.

Palette to draw from (invent others the diff suggests): security, performance, framework conventions, correctness, edge cases, concurrency, API design, data integrity, accessibility, error handling, test coverage, UX. State each lens's focus precisely so the reviewer hunts this diff's failure modes, not generic advice.

Afterward: verify and deduplicate findings against the code, report the valid ones grouped by lens with anything unverified marked, then present a fresh picker whose first suggestion(s) turn the highest-severity findings into work.

## Standalone standards & spec review

Two fixed axes, reported side by side. A change can pass one and fail the other — code that follows every standard but implements the wrong thing, or code that does what the issue asked while breaking conventions — so the axes are never merged or reranked into a single verdict.

1. **Pin the fixed point.** Whatever the developer names (SHA, branch, tag, `main`, `HEAD~5`). Confirm it resolves (`git rev-parse`), capture `git diff <point>...HEAD` (three-dot, against the merge-base) and `git log <point>..HEAD --oneline`. A bad ref or empty diff fails here, not inside the sub-agents.
2. **Find the spec**: issue references in the commit messages, a path the developer passed, or a spec file under `docs/`/`specs/` matching the branch. If none exists, the Spec axis reports "no spec available" instead of running.
3. **Find the standards**: whatever documents how code should be written here (`CONTRIBUTING.md`, `CODING_STANDARDS.md`, `CLAUDE.md`). On top, the Standards reviewer carries a smell baseline — Fowler's classics (mysterious name, duplicated code, feature envy, data clumps, primitive obsession, repeated switches, shotgun surgery, divergent change, speculative generality, message chains, middle man) — pasted into its prompt, each flagged as a judgement call. A documented repo standard overrides the baseline; skip anything tooling already enforces.
4. **Dispatch both axes in parallel** through the machine above. Standards reports documented-standard violations (cite the rule) and baseline smells (name it, quote the hunk). Spec reports missing or partial requirements, scope creep, and requirements implemented wrong, quoting the spec line for each.
5. **Report** under `## Standards` and `## Spec` headings, with a one-line total per axis and the worst finding within each. Inside a vibe session, follow with a picker that turns the top findings into work; invoked standalone, the report is the deliverable.
