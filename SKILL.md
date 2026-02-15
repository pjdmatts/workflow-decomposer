---
name: workflow-decomposer
description: >
  Decomposes task descriptions into atomic skills using the Role-Verb-Noun-Output pattern.
  Use when the user wants to: (1) Break down a complex task into automatable steps,
  (2) Identify which parts of work can use AI, (3) Design a workflow or skill,
  (4) Understand task complexity (Prompt vs Workflow vs Agent), (5) Create skills from task descriptions.
  Triggers on phrases like "decompose this task", "break this down", "what skills do I need",
  "is this a prompt or workflow", "help me automate", "create a skill for", "workflow for".
---

# Workflow Decomposer

Analyze tasks using the **Role – Verb Noun → Output (Done-when?)** pattern from [Prompt, Workflow, Agent](https://peter-matthews.com/blog/prompt-workflow-agent/).

## Process

When given a task description:

### 1. Identify the Work Product

What gets "shipped"? Examples: report, email, summary, presentation, data file, blog post.

### 2. Decompose into Atomic Tasks

For each atomic task, fill in this pattern:

| Component | Question | Example |
|-----------|----------|---------|
| **Role** | Who performs this? | Research Assistant |
| **Verb** | What action? | Summarize |
| **Noun** | On what? | Meeting notes from last week |
| **Source** | Where does the input come from? | Previous task output, user upload, API |
| **Output** | What format/deliverable? | Five bullet points |
| **Done-when** | Completion criteria? | All key decisions captured |

### 3. Identify GenAI-Friendly Verbs

Flag verbs by confidence level:

- **High**: Summarize, Extract, Classify, Rewrite, Translate, Generate, Format
- **Medium**: Compare, Analyze, Explain, Convert, Merge, Split
- **Lower**: Decide, Evaluate, Create (original), Research (requires tools)

### 4. Assess Complexity

| Pattern | Complexity Level | Characteristics |
|---------|------------------|-----------------|
| Single task | **Prompt** | One input → one output |
| Sequential tasks | **Workflow** | Chain of prompts, each feeds next |
| Branching/conditionals | **Agentic Workflow** | "If X then Y" logic, routing |
| Open-ended + tools | **Agent** | Planning, memory, external actions |

### 5. Output the Decomposition

```markdown
## Task Decomposition: [Original Task]

**Work Product**: [What gets shipped]
**Complexity**: [Prompt | Workflow | Agentic Workflow | Agent]

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | ... | ... | ... | ... | ... | ... |
| 2 | ... | ... | ... | ... | ... | ... |

**GenAI-Friendly Verbs Found**: [list with confidence levels]
**Dependencies**: [which tasks depend on others]
**Recommended Approach**: [brief explanation]
```

### 6. Generate Skill Templates (if requested)

For each atomic task that warrants its own skill, generate a SKILL.md template:

```yaml
---
name: [verb]-[noun-slug]
description: >
  [Role] that [verb]s [noun] to produce [output].
  Use when: [trigger conditions].
---

# [Skill Name]

## Input
[What this skill expects]

## Process
[Steps to complete the task]

## Output
[Format and done-when criteria]

## Example
[Concrete example of input → output]
```

## Example Decomposition

**Input**: "Create a weekly blog post from meeting notes"

---

## Task Decomposition: Weekly Blog Post from Meeting Notes

**Work Product**: Published blog post (~800 words)
**Complexity**: Workflow (3 sequential tasks)

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Analyst | Extract | Meeting notes (all from week) | User-provided files | Key themes, decisions, notable quotes | All meetings covered, themes identified |
| 2 | Writer | Generate | Extracted themes + context | Task 1 output | Draft blog post (800 words) | Coherent narrative, all themes addressed |
| 3 | Editor | Rewrite | Draft post + style guide | Task 2 output + style guide ref | Polished final post | Passes editorial checklist, matches voice |

**GenAI-Friendly Verbs Found**: Extract (high), Generate (high), Rewrite (high)

**Dependencies**:
- Task 2 requires output from Task 1
- Task 3 requires output from Task 2
- Task 3 benefits from style guide reference

**Recommended Approach**:
Workflow with 3 chained skills. Create separate skills for Extract and Rewrite (reusable). The Generate step is task-specific. Consider a `references/style-guide.md` for Task 3.

---

## Workflow vs Skill Decision

After decomposition, decide whether to create:

1. **Single skill** — If tasks are tightly coupled and always run together
2. **Multiple skills** — If atomic tasks are reusable across different workflows
3. **Workflow orchestration** — If you need conditional logic or human checkpoints

For complex workflows, see [references/workflow-patterns.md](references/workflow-patterns.md).
