# Execution Workflow

## Default Loop

When using an agent on this project, the default workflow is:

1. Clarify the task and define what "done" means.
2. Gather local context before proposing changes.
3. Point the agent at relevant examples.
4. Ask for a small implementation step or draft.
5. Run checks or perform manual verification.
6. Review the diff and explanation.
7. Refine or expand only after the previous step is trustworthy.

This keeps the work evidence-driven and reduces false confidence.

## Start With Context, Not Output

Before asking an agent to write or change anything:

- Inspect the existing files.
- Identify similar patterns in the repo.
- Find the constraints that matter.
- Surface unknowns early.

Agents do better when they inherit context from the actual project instead of from generic assumptions.

## Use Testing-Driven Development For Prompts

The guide's TDD chapter extends beyond unit tests. The broader lesson is to define the observable behavior you want before iterating on the agent instructions.

Practical applications for this repo:

- For code: write or identify the test, expected output, or acceptance behavior first.
- For docs: define the audience, structure, tone, and specific questions the doc must answer.
- For scripts or migrations: define input, output, and failure conditions before asking for implementation.

If a prompt is hard to evaluate, the task definition is probably still weak.

## Run The Thing Early

One of the strongest recurring patterns in the source material is simple: do not stop at generated code. Run it.

For project work:

- Execute tests when they exist.
- Run linters or type checks when relevant.
- Open the generated artifact when the task is user-facing.
- Inspect logs or traces for debugging work.
- Record what was actually verified.

Passing generation is not evidence. Runtime behavior is evidence.

## Manual Testing Is Part Of The Workflow

The manual testing chapter argues that agents are especially useful when paired with fast human validation.

Use manual testing when:

- Behavior is visual or experiential.
- Edge cases are easier to probe interactively than to codify first.
- You need to compare multiple variants quickly.
- The work is exploratory and acceptance criteria are still forming.

Good manual testing habits:

- Write a concise test script before starting.
- Capture the environment and input conditions.
- Note expected versus actual behavior.
- Convert repeated manual checks into automated checks when they stabilize.

## Ask For Diff Explanations

Review quality goes up when the agent explains what changed and why.

Useful review questions:

- What files changed and for what reason?
- What assumptions did you make?
- What was not changed?
- What risks remain?
- What did you verify, exactly?

This is especially useful for generated work because it exposes weak reasoning and hidden scope expansion.

## Keep Humans In The Tight Loop

The source material makes a strong case for interactive collaboration over full autonomy. The most reliable pattern is:

- Agent proposes.
- Human inspects.
- Agent revises.
- Human verifies.

That loop is usually better than asking for one giant finished answer and trusting it.

## Use Interactive Explanations When Understanding Matters

If the team does not understand the system, the agent's speed does not help much. The interactive explanations chapter emphasizes using AI to build understanding, not just artifacts.

For this project, that means asking agents to:

- Explain how a file or subsystem works.
- Show alternative implementations and tradeoffs.
- Turn dense logic into plain-language walkthroughs.
- Generate small demos or examples that make behavior obvious.

Understanding is a delivery accelerator because it reduces bad edits later.

## Stop When Productivity Turns Hollow

Pause and reset the workflow if any of these happen:

- The agent is generating lots of output without reducing uncertainty.
- The same task keeps being re-prompted with no sharper acceptance criteria.
- The diff is growing faster than confidence.
- Verification is repeatedly deferred until "later."

At that point, the right move is usually to narrow scope, restate the task, or return to direct human design work.

## Project Checklist

Use this checklist before considering an agent-assisted task complete:

- The task objective is stated clearly.
- Relevant local examples were consulted.
- The resulting change is scoped and explainable.
- Verification was performed and recorded.
- The remaining risks are known.
- The human reviewer would be comfortable owning the result.
