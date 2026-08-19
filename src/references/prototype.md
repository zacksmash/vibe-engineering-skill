# Prototype

A prototype is **throwaway code that answers a design question**. It runs standalone as its own mode, or as a spike offered mid-grilling when a plan branch only resolves by seeing it.

## The question decides the shape

Grilling pins one thing: what design question must this answer? That routes to a branch:

- **"Does this logic / state model feel right?"** → a single shareable HTML file with free-play controls plus a guided walkthrough, pushing the state machine through the cases that are hard to reason about on paper. A non-developer can drive it.
- **"What should this look like?"** → several radically different UI variations on one route, switchable via a URL search param, using whatever routing convention the project already has.

## Rules

1. **Throwaway from day one, and marked as such.** Build it in a throwaway worktree or branch, named so a casual reader sees it's a prototype. Prototype code never merges into real session work; only the validated decision does.
2. **Trivial to run.** One command from the project's task runner, or an HTML file the developer double-clicks. Zero thinking to start it.
3. **No persistence.** State lives in memory. If persistence *is* the question, use a scratch store with a clear "PROTOTYPE, wipe me" name.
4. **Skip the polish.** No tests, no abstractions, error handling only to stay runnable. The point is to learn fast.
5. **Surface the state.** After every action (logic) or variant switch (UI), show the full relevant state so the developer sees what changed.
6. **End with a verdict.** Record the question and its answer where the work continues: in the plan for a mid-grilling spike, in the conversation for a standalone run. Ask before deleting the prototype.

## The mid-grilling dance

Plan Mode is read-only, so a spike steps out and back: exit Plan Mode with a narrow prototype proposal, wait for approval, build it, collect the verdict, then re-enter Plan Mode and revise the plan with the answer.
