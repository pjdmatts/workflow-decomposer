# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Claude Code skill called `workflow-decomposer`. It decomposes complex task descriptions into atomic, automatable workflow steps using a structured **Role – Verb Noun → Output (Done-when?)** pattern. The project consists entirely of Markdown documentation — there is no compiled code, package manager, or build system.

## Key Files

### Skill files (what users install)

- `SKILL.md` — Core skill definition with YAML frontmatter and process instructions
- `tutorial.md` — User-facing guide: prerequisites, quick start, worked examples, best practices
- `references/workflow-patterns.md` — Detailed workflow pattern reference (linear chain, fan-out/fan-in, conditional, loop, human-in-the-loop)

### Development docs (not part of the installed skill)

- `docs/3.5-workflow-decomposition-skill.md` — Project specification with success criteria and implementation details
- `docs/Work Decom Agent.md` — Reference agent definition with YAML frontmatter and the formal pattern template
- `docs/test-log.md` — Validation results for three complexity levels (Prompt, Workflow, Agent); all passing

## Architecture

### Core Pattern

Every atomic task follows this structure:
```
Role – Verb Noun (from: Source) → Output (Done-when?)
```

### Complexity Ladder

Tasks are classified into four levels based on their decomposition:

| Level | Characteristics |
|-------|----------------|
| **Prompt** | Single input → single output (1 atomic task) |
| **Workflow** | Multiple sequential tasks; linear chain or fan-out/fan-in |
| **Agentic Workflow** | Branching/conditional logic ("if X then Y") |
| **Agent** | Open-ended goals requiring planning, memory, tool use, state |

### GenAI Verb Confidence

Verbs are scored by how reliably AI can perform them:
- **High**: Summarize, Extract, Classify, Rewrite, Translate, Generate, Format
- **Medium**: Compare, Analyze, Explain, Convert, Merge, Split
- **Lower**: Decide, Evaluate, Create (original), Research (web)

### Workflow Patterns

The skill recognizes and suggests orchestration patterns: linear chain, fan-out/fan-in (parallel), conditional branching, loop-with-condition, and human-in-the-loop checkpoints.
