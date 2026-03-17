# Prompt Patterns

## Prompt Design Principle

The guide's annotated prompt material and linked notes argue that the best prompt is often not a clever paragraph. It is a combination of:

- Clear goal.
- Real project context.
- Concrete examples.
- Explicit constraints.
- Tool access.
- A review and verification loop.

This document turns that into reusable patterns for this repo.

## Pattern 1: Context Before Action

Use this when the task touches existing project material.

Prompt shape:

```text
Understand the existing implementation before changing anything.
Inspect the relevant files first, summarize the current pattern, then propose the smallest change that satisfies the goal.
Do not assume structure that you have not verified in the repo.
```

Why it works:

- It pushes the agent into tool use.
- It reduces speculative edits.
- It creates a natural checkpoint before implementation.

## Pattern 2: Give The Agent One Strong Example

Use this when consistency matters more than novelty.

Prompt shape:

```text
Use [file/example] as the canonical example for structure, tone, and level of detail.
Match that pattern unless there is a clear reason to diverge, and explain any divergence.
```

Why it works:

- It uses imitation, which agents are good at.
- It avoids long style instructions.
- It produces more consistent results across repeated tasks.

## Pattern 3: Ask For Evidence, Not Confidence

Use this when correctness matters.

Prompt shape:

```text
After making changes, run the relevant checks and report exactly what you verified.
If you could not verify something, say so explicitly rather than inferring success.
```

Why it works:

- It replaces vague assurance with observable evidence.
- It creates a clear distinction between verified and unverified claims.

## Pattern 4: Require A Diff Narrative

Use this for review-heavy work.

Prompt shape:

```text
When you finish, summarize the diff in plain language:
- what changed
- why it changed
- what was intentionally left unchanged
- what risks or follow-ups remain
```

Why it works:

- It makes review faster.
- It reveals accidental scope creep.
- It forces the agent to model its own edits.

## Pattern 5: Constrain Scope Aggressively

Use this when the task could expand unpredictably.

Prompt shape:

```text
Limit changes to the smallest set of files needed.
Do not refactor adjacent areas unless the task cannot be completed safely otherwise.
If broader cleanup seems useful, note it separately instead of bundling it in.
```

Why it works:

- It keeps work inspectable.
- It reduces surprise.
- It protects the repo from "while I was here" drift.

## Pattern 6: Use Thinking Budget Deliberately

The guide and linked Claude Code note both point to a practical tactic: ask for more deliberate reasoning only when the task justifies it.

Use higher-effort prompts for:

- Ambiguous architecture decisions.
- Risky migrations.
- Tradeoff analysis.
- Debugging with several plausible causes.

Do not spend extra reasoning budget on:

- Routine formatting.
- Straightforward content transformations.
- Obvious small edits.

More thinking should be targeted, not habitual.

## Pattern 7: The Artifact Is Part Of The Prompt

One linked note from the guide highlights a useful idea: the thing you are building can itself shape future prompts. A high-quality artifact becomes a reusable instruction.

Project rule:

- When a generated doc, spec, script, or template turns out well, preserve it as a reference for similar future work.

That creates compounding returns on good output.

## Pattern 8: Prefer Reusable Project Instructions Over Repeated Prompt Text

If the same instruction appears in many prompts, it probably belongs in project documentation or agent instructions instead.

Good candidates:

- Repo-specific style rules.
- Required verification steps.
- Review expectations.
- Naming or documentation conventions.

This reduces prompt sprawl and makes behavior more consistent across sessions.

## Prompt Anti-Patterns

Avoid prompts that:

- Ask for a complete solution without giving repo context.
- Encourage broad autonomy without checkpoints.
- Reward speed without verification.
- Mix exploration, implementation, and approval into one step.
- Ask the agent to "make it better" without defining better.

These patterns create output quickly, but they make trust expensive.

## Reusable Review Prompt

Use this when you want an agent to explain and self-audit a change:

```text
Review your own changes before presenting them.
Explain:
1. What changed.
2. Why this approach was chosen over obvious alternatives.
3. What you verified.
4. What remains uncertain.
5. Any follow-up work that should be kept out of this diff.
```

## Reusable Exploration Prompt

Use this when you need understanding before deciding:

```text
Explore the relevant files and summarize the existing patterns, constraints, and likely implementation options.
Do not edit anything yet.
Call out unknowns and the smallest viable next step.
```

## Reusable Implementation Prompt

Use this when the task is ready for execution:

```text
Implement the smallest complete change that satisfies the requirement.
Match existing patterns in the repo.
Use tools to verify assumptions before editing.
After editing, run relevant checks and summarize the diff and verification results.
```
