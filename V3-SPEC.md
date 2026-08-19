Vibe Engineer

Vibe engineer has many modes.

—

Implementation: triggered by “let’s build” or “let’s work on”, someway the user is hinting that this is a new feature they want to build

One path is the implementation feature. This is where the user describes their feature in the initial prompt. The agent then begins the “grilling” or interview section where they relentlessly interview the developer about anything they don’t understand or are fuzzy on with the current task. This process involves producing multiple question popups with 4 suggested answers, one recommendation at the top, and a free-form input field if the user needs to describe something else. I’m not sure if plan mode starts before grilling or after grilling, let’s discuss.

After Grilling, the agent is in plan mode and presents the user with the plan. The user reviews the plan and suggests edits, or approves to move forward with it.

When the user has approved the plan, the agent goes into code mode. There are 3 different types of Implementation modes.

Step Mode (default): In this mode, the agent produces 4 logical suggested next steps in the question UI picker (AskUserQuestion or equivalent). Suggestions should always follow the trajectory of the project, they should never suggest something that’s already done, or something that’s too far down the road. They should be meaningful and provide a way to move the codebase forward right now. For example, at the beginning, we might be installing frameworks and dependencies, at the end, we might be writing tests. Always 4 intelligent, logical next steps as suggestions.Flow Mode: Similar to step mode, but with a little more leash. The agent can handle 2-4 steps at a time, instead of step-by-step. The user is comfortable with the direction that has been set and is willing to allow the agent to push things further and faster. Same suggestion framework when it’s finished with the current task, but bigger scopes.Agent Mode: This is your standard mode in most coding harnesses. The user provides a task, the agent grills them, and a plan is produced. Once approved, the agent builds everything, top to bottom without stopping. The agent is allowed to pause if they have questions along the way where they produce the same 4 suggestions in a picker for the user, but otherwise it’s full steam ahead. Regardless of which mode is set, after each step/suggested step, the agent is required to detail to the user what they did in the conversation feed, if the project is still unfinished, the agent should produce the next set of suggested steps, which could include things like Commit, PR, etc. Suggestions should not drag on, if the task is finished, let’s wrap it up. Only produce suggestions that are part of the users description and push the task forward, or things you’ve discovered along the way that wold fit in perfectly to the features.

Along the way of any of these modes, the agent should use the automated workflow, or just mention using workflows to dispatch any number subagents to adversarially review  a batch of code. When the tracked changes feels substantial and warrants a review only. We shouldn’t be reviewing a simple change or small set of files. Only when the complexity of the changes could benefit from reviews. Reviews should be done from multiple lenses, i.e. Security, Performance, UX, Conventions, etc. The model should determine which lenses makes most sense given the changes.

—

Prototype: Build a throwaway prototype to answer a design question. Use when the user wants to sanity-check whether a state model or logic feels right, or explore what a UI should look like.

—

Research: Investigate a question against high-trust primary sources and capture the findings as a Markdown file in the repo. Use when the user wants a topic researched, docs or API facts gathered, or reading legwork delegated to a background agent.

—

Triage/Bug Diagnosis:  Diagnosis loop for hard bugs and performance regressions. Use when the user says "diagnose"/"debug this", or reports something broken/throwing/failing/slow.

—

Adversarial Review: Review the changes since a fixed point (commit, branch, tag, or merge-base) along two axes: Standards (does the code follow this repo's documented coding standards?) and Spec (does the code match what the originating issue/spec asked for?). Runs both reviews in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to "review since X".
