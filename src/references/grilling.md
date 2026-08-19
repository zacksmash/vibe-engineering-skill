# Grilling

The interview method every mode calls at its own intensity. The goal is a shared, concrete design; the developer should feel productively interrogated, never pestered.

## The method

Map the open decisions as a **design tree**: every decision branches into the decisions that hang off it. Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled, the questions you can ask now without guessing at answers you haven't heard. Ask the whole frontier in one round, then wait; each round of answers pushes the frontier outward.

- **Recommend an answer for every question**, and put it first. In the picker, the recommended answer is option 1. A genuinely open-ended question goes as free text with a stated recommendation.
- **Facts are your job; decisions are the developer's.** Look up anything the environment can answer (dispatch a sub-agent if needed) and ask only what requires their judgement.
- **Challenge fuzzy vocabulary.** "You said 'account' — Customer or User? Those are different things here." Pin every term to what the codebase actually calls it.
- **Offer a skill when one fits the question.** When a planning question matches an installed skill's trigger, the recommended answer invokes it by name (mechanism in `suggestions.md`).
- **Offer a prototype when talking can't answer.** Some branches only resolve by seeing them ("does this state model feel right?"). Propose a spike per `prototype.md`.

## Exit

Stop grilling when no blocking, high-impact, or hard-to-reverse decision remains ambiguous. State minor reversible choices as defaults and resolve them later in the loop. If the developer says "just plan it" or "stop grilling": answer the remaining important decisions with your recommendations, mark them as assumptions, and present the plan. That phrase ends the interview only; plan approval remains the implementation gate.

## Intensity per mode

| Mode | What to grill |
|---|---|
| Implementation | Full method, until the exit condition above holds. |
| Diagnosis | Facts only: exact symptom, repro steps, expected vs actual, last known good, recent changes. |
| Research | One scoping round: the question, what proof settles it, where the findings file lives. |
| Prototype | One question: what design question must this prototype answer? Plus the logic-vs-UI branch. |
| Adversarial review | Pin the fixed point ("review since what?") and locate the spec, then go. |
