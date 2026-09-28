---
name: exercise
description: Turn a completed AI, ML, RL, or software-engineering lesson recorded in a Markdown file into a focused 1–5 hour portfolio-quality coding exercise, write its EXERCISE.md handoff, and launch one selected coding CLI to create learner-ready starter code. Use when the user invokes exercise with a learning Markdown path and a destination project directory or asks to practice, implement, or build something based on a lesson they just completed.
---

# Exercise

Convert recently learned material into a small project in which the learner implements the important idea, not the surrounding plumbing.

Accept two positional inputs:

1. `learning_markdown_path`: the `.md` file containing the completed lesson.
2. `exercise_directory`: the directory where the starter project and `EXERCISE.md` must live.

Treat invocations such as `$exercise "notes/dqn.md" "projects/dqn-cartpole"` as providing those inputs. Resolve paths from the current working directory. If an input is missing, ask only for the missing value. Require the lesson path to be a readable Markdown file. The destination may be new or existing; inspect an existing destination before planning and preserve unrelated contents.

## 1. Understand the lesson

Read the complete lesson. Identify:

- the central mechanism the learner should now implement;
- supporting concepts they already learned;
- prerequisite setup that would waste practice time;
- likely misconceptions the exercise can expose;
- a visible result that would make the finished project satisfying to demonstrate.

If the lesson spans several topics, choose the smallest coherent subset that produces meaningful practice. Ask a clarifying question only when the intended learning target cannot be inferred reliably.

## 2. Design the exercise

Create one concrete project, not a menu of vague ideas. Optimize for all of these constraints:

- The core version takes roughly 1–5 hours for the learner.
- It can become a credible AI/ML/RL/SWE/engineering portfolio project with later extensions.
- The learner writes the concept they are practicing.
- The scaffold supplies incidental work: environment setup, dependency declarations, data acquisition, parsing, data loaders, train/evaluation loops, visualization, interfaces, fixtures, and other boilerplate as appropriate.
- Use established public datasets, environments, APIs, libraries, and official or reputable boilerplate when they reduce irrelevant setup. Research current options when needed and record source links in the handoff.
- Avoid a toy wrapper whose only challenge is filling in one obvious line. Give the learner enough integration, debugging, and evaluation responsibility to understand the concept.
- Avoid unnecessary product surface such as authentication, deployment, databases, or a frontend unless it materially supports the lesson.
- Keep the target implementation genuinely learner-owned. Do not hide a completed solution in helpers, tests, comments, generated artifacts, or copied reference code.

For example, an MSE-loss lesson should arrive with data generation or loading, batching, a small model, training/evaluation plumbing, and tests already scaffolded. The learner should implement and integrate the loss behavior being practiced rather than first inventing a dataset pipeline.

## 3. Present the plan and pause

Before creating the destination, writing `EXERCISE.md`, or changing code, present a concise plan that includes:

- a compelling project title and a two- or three-sentence pitch that makes the result feel worth building;
- the final demo or observable outcome;
- the precise functions, classes, algorithms, or components the learner will implement;
- what the starter scaffold will provide;
- the expected 1–5 hour core scope;
- acceptance criteria and optional portfolio extensions.

Be energetic and specific about why the project is interesting, but do not use hype without substance.

Then ask for both decisions at the same checkpoint:

1. Confirm the plan: `Approve and scaffold` (recommended), `Revise this plan`, or `Choose a different project`.
2. Choose who creates the starter code, using exactly these four choices: `claude`, `codex`, `agy (Antigravity)`, or `Me (the Exercise author)`.

Use the harness's interactive multiple-choice tool when it supports four choices. Otherwise show a numbered four-choice prompt and require one selection. Do not proceed until the user approves the plan and chooses an author. Revise and re-confirm when requested.

## 4. Write the handoff

After approval, create the destination directory if necessary and write `<exercise_directory>/EXERCISE.md`. This Markdown file is both the learner brief and the implementation contract for the starter-code author. Include:

1. **Project and payoff** — what is being built and what the learner can demo.
2. **Learning objective** — the exact ideas from the lesson that the exercise reinforces.
3. **Core user story or experiment** — the behavior of the finished project.
4. **Learner-owned implementation** — exact files, symbols, or components reserved for the learner, with expected inputs, outputs, invariants, and constraints but no solution.
5. **Provided scaffold** — setup, datasets, loaders, environments, utilities, evaluation, visualization, and tests the starter-code author must supply.
6. **Milestones and timebox** — a short path through the 1–5 hour core exercise.
7. **Acceptance criteria** — observable behavior and commands for checking the work.
8. **Run instructions** — environment setup and the commands the scaffold should make available.
9. **Hints** — conceptual nudges arranged from light to stronger, never a pasted solution.
10. **Extension path** — a few optional improvements that could turn the core exercise into a stronger portfolio piece.
11. **Sources** — dataset, environment, API, or boilerplate links actually used.
12. **Starter-author contract** — the rules below, repeated clearly for the coding agent.

The starter-author contract must require the author to:

- create only the learner-ready scaffold, not solve learner-owned tasks;
- make setup and data acquisition automatic or clearly scripted;
- provide a useful project structure, dependency metadata, entry point, tests or checks, and concise project documentation when appropriate;
- mark implementation gaps consistently with `TODO(learner)`;
- ensure failures point directly to intentional learner tasks rather than missing plumbing;
- keep the project runnable, importable, or compilable as far as the chosen ecosystem permits before those TODOs are completed;
- avoid embedding the solution in tests, comments, notebooks, generated files, or commit history;
- leave `EXERCISE.md` intact;
- stop after producing and checking the starter state.

Do not create source code, tests, dependency files, datasets, or other project files while acting as the exercise author. Apart from making the directory, the only file the exercise author may write is `EXERCISE.md`. The exception is when the user selected `Me (the Exercise author)` in the approval checkpoint.

## 5. Create the starter code

### External CLI selected

For `claude`, `codex`, or `agy (Antigravity)`, launch exactly one autonomous, non-interactive CLI turn with the exercise directory as its working directory. The prompt must tell the selected agent to read `EXERCISE.md`, obey its starter-author contract, create and verify the scaffold, and stop without implementing any `TODO(learner)` work.

Before launching, verify the requested executable is installed and inspect its local `--help` output to choose the currently supported single-turn/non-interactive invocation. Map the choices to executables as follows:

- `claude` → `claude`
- `codex` → `codex`
- `agy (Antigravity)` → `agy`

Use documented automation and workspace flags only. Do not disable safety controls or grant permissions beyond the destination directory merely to make the launch succeed. If the executable is absent, the invocation syntax cannot be determined safely, or the CLI requests unavailable authentication or approval, stop and give the user the exact blocker; do not switch agents silently and do not write the scaffold yourself.

Capture the CLI's exit status and final output. Do not launch a second agent turn automatically. The user can follow up with that agent later.

### `Me (the Exercise author)` selected

Act as the single starter-code author after writing `EXERCISE.md`. Follow the same starter-author contract, create the scaffold, run its setup-independent checks, and stop with every learner-owned implementation still marked `TODO(learner)`.

## 6. Verify and hand off

Inspect the resulting project regardless of author. Verify that:

- `EXERCISE.md` exists in the requested directory;
- the learner-owned work is not already implemented;
- the supporting boilerplate promised by the plan exists;
- setup and run commands are consistent with the generated files;
- available fast checks pass, except for failures intentionally tied to `TODO(learner)` items;
- the core exercise still fits the 1–5 hour target.

Fix only the handoff itself when the external author misunderstood an instruction. Do not fill in missing scaffold code on the external author's behalf; report the gap and the agent's output so the user can choose whether to follow up.

End with the project path, the handoff path, the selected author, what was scaffolded, the learner's first concrete task, and any verification result or blocker.
