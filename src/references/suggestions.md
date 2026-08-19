# Suggestions

The 4 picker suggestions are the developer's primary navigation: they predict the next move. One question, 4 options. The developer may answer with just a number; "2" means "do suggestion #2".

## Four inputs

Craft every set from:

1. **The approved plan** — what remains between here and the goal.
2. **The session file set** — what exists right now, so nothing already built is re-suggested.
3. **The last step** — what it just unblocked or revealed.
4. **The installed-skill inventory** — when a slot's work matches an installed skill's trigger, phrase the suggestion as invoking that skill by name: "Run /tdd for the invite flow". The skill is the *how* of a slot, never a filler *what*. During grilling, the same rule surfaces planning skills (prototype, research, domain-modeling and kin).

## Rules

- **Ordered by dependency.** Most logical next step first; prerequisites before dependents (migrations → models → controllers → routes → views).
- **Every slot advances the outcome or confidence**: code, a test, validation, or documentation the feature requires. Adversarial review takes slot 1 when the core loop's substantial-work trigger fires; a finish/PR option appears only once the goal is met.
- **Concrete and atomic.** "Add TeamController store action with validation and a policy check", never "build TeamController" or "work on the next step".
- **Predict with senior instincts.** Surface what the developer might miss: a forgotten index, a needed policy, an a11y or edge case. Suggest only work that pushes the described feature forward or fits it naturally; when the task is finished, wrap up rather than padding.
- **Evolve as the feature matures.** Early: structure. Later: tests, authorization, validation, error handling, performance, docs.
- **Reuse over reinvent.** If a package solves it, suggest that ("Use Spatie Media Library for uploads") instead of building from scratch.

## Picker mechanics

A blocking clarification uses one decision picker *before* work and replaces the suggestion picker until answered. Terminal states (finishing, pausing, a hard error) use their own state-appropriate prompt with only the real options; filler options are never invented anywhere.
