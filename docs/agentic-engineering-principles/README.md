# Agentic Engineering Principles

This folder adapts Simon Willison's "Agentic Engineering Patterns" guide into project documentation for this repository. It is not a verbatim mirror of the source. Instead, it turns the guide's pages and linked subpages into a practical set of principles, workflows, and prompt patterns that can guide how we use coding agents on this project.

## Goal

Use agents to increase engineering throughput without lowering standards. The operating model here is:

- Let agents do large amounts of mechanical work quickly.
- Keep humans responsible for intent, judgment, review, and final quality.
- Build workflows that make iteration cheap but verification mandatory.

## How To Use This Folder

- Read [principles.md](/Users/sam/loadstar/docs/agentic-engineering-principles/principles.md) for the core beliefs and guardrails.
- Read [execution-workflow.md](/Users/sam/loadstar/docs/agentic-engineering-principles/execution-workflow.md) for the default delivery loop when working with agents.
- Read [prompt-patterns.md](/Users/sam/loadstar/docs/agentic-engineering-principles/prompt-patterns.md) for reusable prompting tactics and project instruction patterns.

## Project Position

This repository is documentation-first right now, so these principles are written to be broadly applicable. As code, tests, and deployment surfaces appear, these docs should be tightened further around the actual toolchain and review practices in use.

## Source Coverage

The material in this folder is derived from the guide page and its subpages, including:

- The guide overview:
  - https://simonwillison.net/guides/agentic-engineering-patterns/
- Guide chapters:
  - https://simonwillison.net/guides/agentic-engineering-patterns/what-is-agentic-engineering/
  - https://simonwillison.net/guides/agentic-engineering-patterns/how-coding-agents-work/
  - https://simonwillison.net/guides/agentic-engineering-patterns/writing-code-is-cheap/
  - https://simonwillison.net/guides/agentic-engineering-patterns/hoard-your-examples/
  - https://simonwillison.net/guides/agentic-engineering-patterns/testing-driven-development/
  - https://simonwillison.net/guides/agentic-engineering-patterns/make-a-habit-of-using-tools/
  - https://simonwillison.net/guides/agentic-engineering-patterns/harmful-productivity/
  - https://simonwillison.net/guides/agentic-engineering-patterns/julius-agentic-coding/
  - https://simonwillison.net/guides/agentic-engineering-patterns/annotated-prompts/
  - https://simonwillison.net/guides/agentic-engineering-patterns/interactive-explanations/
  - https://simonwillison.net/guides/agentic-engineering-patterns/agentic-manual-testing/
- Related linked notes surfaced by the guide:
  - https://simonwillison.net/2025/Apr/19/claude-code-best-practices/
  - https://simonwillison.net/2025/May/15/openai-gpt-4_1-prompting-guide/
  - https://simonwillison.net/2025/Jun/29/harmful-productivity/
  - https://simonwillison.net/2025/Jul/18/the-artifact-is-the-prompt/
  - https://simonwillison.net/2025/Sep/11/replacing-my-blog-with-a-180kb-sqlite-file-and-some-open-source-tools/

## Maintenance Rule

If this folder starts to drift from how the team actually works, update the docs to match reality. Agentic engineering guidance that does not reflect the real repo workflow becomes noise quickly.
