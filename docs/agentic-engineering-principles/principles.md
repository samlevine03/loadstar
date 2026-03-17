# Core Principles

## 1. Treat Agentic Engineering As Supervised Delegation

Agentic engineering is not "tell the AI to build the product." It is a way of delegating bounded engineering work to a tool that can read, write, run commands, and iterate.

For this project, that means:

- Humans decide goals, constraints, and acceptance criteria.
- Agents perform exploration, drafting, implementation, refactoring, and repetitive transformation work.
- Humans remain accountable for correctness, architecture, security, and shipping decisions.

The agent is a fast collaborator, not an authority.

## 2. Understand What Coding Agents Actually Do

Coding agents are useful because they operate in a loop:

1. Read the current context.
2. Decide on the next action.
3. Use tools to inspect files, edit code, or run commands.
4. Observe the results.
5. Repeat until they think the task is done.

That loop makes them more capable than a plain chat model, but it also creates failure modes:

- They can follow the wrong plan with high confidence.
- They can overfit to local signals like a passing test.
- They can make broad edits that look coherent but weaken the system.

Our process should assume those risks are normal.

## 3. Writing Code Is Cheap Now

The guide's central shift is that code generation is no longer the scarce part of engineering. Iteration is cheap. Judgment is not.

Project implications:

- Explore multiple approaches when the design is unclear.
- Prefer fast prototypes over long speculative debates.
- Throw away weak implementations quickly.
- Spend saved time on review, testing, and clearer product decisions.

Cheap code generation should increase standards, not reduce them.

## 4. Examples Are A Strategic Asset

One of the strongest repeated themes in the source material is that good examples dramatically improve agent performance. Agents imitate patterns better than they infer them from abstract rules.

For this project:

- Preserve strong examples of docs, tests, interfaces, and naming.
- Point agents at the best local example before asking for a similar change.
- When a prompt works well, save it.
- When a generated result is excellent, keep it as future reference material.

If we want better output, we should curate examples instead of rewriting instructions forever.

## 5. Tools Beat Pure Reasoning

Agents improve when they can inspect the repo, run commands, read logs, diff files, and execute tests. The guide's tool-use chapter argues that prompt design should push agents toward evidence, not speculation.

Default rule:

- Prefer "look it up in the repo and verify" over "reason from memory."

Concretely:

- Use search before assuming structure.
- Use diffs before describing changes.
- Use runnable checks before claiming success.
- Use real outputs, logs, and traces when debugging.

The more an agent can ground itself in artifacts, the less hallucination pressure it faces.

## 6. Human Taste Still Matters

The source material repeatedly emphasizes that high-quality outcomes still depend on humans noticing when something is off. The best AI-assisted work is not fully automated. It is tightly steered.

Humans should intervene when:

- The task is under-specified.
- The output is technically correct but awkward.
- The code solves the wrong problem.
- The agent starts making sweeping changes without a strong reason.
- The design needs taste, prioritization, or product judgment.

Human-in-the-loop is not a temporary workaround. It is part of the method.

## 7. Review Is A First-Class Activity

If code is cheap, review becomes more important. The guide highlights practices like asking for diff explanations and using agents to narrate their own changes.

For this project, review should check:

- Did the work match the requested scope?
- Did it preserve existing patterns where that matters?
- Did it introduce complexity without enough payoff?
- Did the agent prove behavior with evidence?
- Would a new team member understand the result quickly?

We should ask agents to explain diffs in plain language, but we should not outsource approval to them.

## 8. Productivity Can Become Harmful

The "harmful productivity" material is a useful warning: agents can create a feeling of progress while quietly increasing entropy.

Warning signs:

- A large amount of output with weak verification.
- Many new abstractions with little simplification.
- Fast feature growth but no stabilization work.
- Repeated rewrites caused by unclear objectives.
- Documentation, code, and behavior drifting apart.

The metric is not "how much the agent produced." The metric is "how much trustworthy progress the project made."

## 9. Prefer Small, Inspectable Steps

Because agents can change a lot quickly, they are easier to manage when tasks are framed as small, inspectable units.

Prefer:

- Clear task boundaries.
- Limited file scopes where possible.
- Intermediate check points.
- Frequent diffs and summaries.
- Short feedback loops.

Large one-shot prompts are acceptable for prototypes, but they are a poor default for mainline engineering work.

## 10. Turn Good Tactics Into System Behavior

Several chapters point to the same conclusion: strong outcomes depend less on one perfect prompt and more on recurring operating habits.

We should encode those habits in:

- Repo documentation.
- Reusable prompt templates.
- Project instructions for agents.
- Checklists for review and testing.
- Example libraries and canonical patterns.

The goal is to make the good path the default path.
