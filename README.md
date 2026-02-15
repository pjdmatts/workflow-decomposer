# Workflow Decomposer

A [Claude Code](https://claude.ai/code) skill that breaks complex task descriptions into atomic, automatable steps using the **Role – Verb Noun → Output (Done-when?)** pattern.

Based on the framework from [Prompt, Workflow, Agent](https://peter-matthews.com/blog/prompt-workflow-agent/).

## What it does

Given any task description, the skill:

1. Identifies the **work product** (what gets shipped)
2. Decomposes it into **atomic tasks** with Role, Verb, Noun, Source, Output, and Done-when
3. Flags **GenAI-friendly verbs** by confidence level
4. Classifies **complexity** (Prompt → Workflow → Agentic Workflow → Agent)
5. Outputs a **structured decomposition table**
6. Optionally generates **SKILL.md templates** for each atomic task

## Installation

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/<your-username>/workflow-decomposer ~/.claude/skills/workflow-decomposer
```

Or copy the skill files manually:

```bash
mkdir -p ~/.claude/skills/workflow-decomposer/references
cp SKILL.md tutorial.md ~/.claude/skills/workflow-decomposer/
cp references/workflow-patterns.md ~/.claude/skills/workflow-decomposer/references/
```

## Usage

### Slash command

```
/workflow-decomposer "Create a weekly blog post from meeting notes"
```

### Natural language

The skill also triggers automatically on phrases like:

- "decompose this task"
- "break this down into steps"
- "what skills do I need for..."
- "is this a prompt or workflow?"
- "help me automate..."

## Example output

```
## Task Decomposition: Weekly Blog Post from Meeting Notes

**Work Product**: Published blog post (~800 words)
**Complexity**: Workflow (3 sequential tasks)

| # | Role    | Verb    | Noun                     | Source             | Output                   | Done-when                        |
|---|---------|---------|--------------------------|--------------------|--------------------------|---------------------------------|
| 1 | Analyst | Extract | Meeting notes (all week)  | User-provided files| Key themes and decisions | All meetings covered             |
| 2 | Writer  | Generate| Extracted themes          | Task 1 output      | Draft blog post (800 wds)| Coherent narrative, themes covered|
| 3 | Editor  | Rewrite | Draft post + style guide  | Task 2 + style ref | Polished final post      | Passes editorial checklist       |

**GenAI-Friendly Verbs Found**: Extract (high), Generate (high), Rewrite (high)
**Dependencies**: Task 2 ← Task 1, Task 3 ← Task 2
**Recommended Approach**: Workflow with 3 chained skills.
```

## Repo structure

```
workflow-decomposer/
├── SKILL.md                    # Core skill definition (install this)
├── tutorial.md                 # User guide with worked examples
├── references/
│   └── workflow-patterns.md    # Workflow pattern reference
├── docs/                       # Development docs (not part of the skill)
│   ├── 3.5-workflow-decomposition-skill.md   # Project spec
│   ├── Work Decom Agent.md                   # Original agent definition
│   └── test-log.md                           # Validation results
├── CLAUDE.md                   # Claude Code project instructions
├── README.md
└── LICENSE
```

Files in `docs/` are development artifacts and aren't needed to use the skill. Only `SKILL.md`, `tutorial.md`, and `references/` are required for installation.

## Learn more

See [tutorial.md](tutorial.md) for a full walkthrough with worked examples at every complexity level.

## License

Apache 2.0 — see [LICENSE](LICENSE).
