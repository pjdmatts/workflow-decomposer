# Workflow Decomposer: Tutorial

A Claude Code skill that breaks any task description into atomic, automatable steps using the **Role – Verb Noun → Output (Done-when?)** pattern.

Based on the framework from [Prompt, Workflow, Agent](https://peter-matthews.com/blog/prompt-workflow-agent/).

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and working
- The `workflow-decomposer` skill installed at `~/.claude/skills/workflow-decomposer/`

## Quick start

Open Claude Code and type:

```
/workflow-decomposer "Create a weekly blog post from meeting notes"
```

You'll get back a structured decomposition table, a complexity assessment, and a recommended approach.

## What the skill does

Given any task description, the skill follows a 6-step process:

1. **Identifies the work product** — what gets "shipped" (a report, an email, a dashboard)
2. **Decomposes into atomic tasks** — each with a Role, Verb, Noun, Source, Output, and Done-when
3. **Flags GenAI-friendly verbs** — rated by confidence (high/medium/lower)
4. **Assesses complexity** — Prompt, Workflow, Agentic Workflow, or Agent
5. **Outputs a decomposition table** — structured markdown you can act on
6. **Generates skill templates** — SKILL.md scaffolds for each atomic task (on request)

## The pattern

Every atomic task follows this formula:

```
Role – Verb Noun (from: Source) → Output (Done-when?)
```

| Component | What it answers | Example |
|-----------|----------------|---------|
| **Role** | Who performs this? | Research Assistant |
| **Verb** | What action? | Summarize |
| **Noun** | On what? | The attached meeting notes |
| **Source** | Where does the input come from? | User upload, previous task, API |
| **Output** | What gets delivered? | Five bullet points |
| **Done-when** | How do we know it's complete? | All key decisions captured |

## Invoking the skill

### Slash command (explicit)

```
/workflow-decomposer "your task description here"
```

### Natural language (automatic)

The skill also triggers on phrases like:

- "decompose this task"
- "break this down into steps"
- "what skills do I need for..."
- "is this a prompt or workflow?"
- "help me automate..."
- "create a skill for..."

So you can simply type:

```
Break this down: onboard a new hire by collecting their info, setting up accounts, and scheduling intro meetings
```

and the skill activates.

## Reading the output

The skill produces a markdown table like this:

```
## Task Decomposition: Weekly Blog Post from Meeting Notes

**Work Product**: Published blog post (~800 words)
**Complexity**: Workflow (3 sequential tasks)

| # | Role     | Verb     | Noun                    | Source              | Output                    | Done-when                         |
|---|----------|----------|-------------------------|---------------------|---------------------------|-----------------------------------|
| 1 | Analyst  | Extract  | Meeting notes (all week)| User-provided files | Key themes and decisions  | All meetings covered              |
| 2 | Writer   | Generate | Extracted themes        | Task 1 output       | Draft blog post (800 wds) | Coherent narrative, themes covered|
| 3 | Editor   | Rewrite  | Draft post + style guide| Task 2 + style ref  | Polished final post       | Passes editorial checklist        |

**GenAI-Friendly Verbs Found**: Extract (high), Generate (high), Rewrite (high)
**Dependencies**: Task 2 ← Task 1, Task 3 ← Task 2
**Recommended Approach**: Workflow with 3 chained skills.
```

Here's how to read each section:

- **Work Product** — the thing that gets shipped at the end.
- **Complexity** — tells you what kind of system you need to build (see complexity ladder below).
- **Table rows** — each row is one atomic task. If a row can't be broken down further without losing meaning, it's atomic.
- **GenAI-Friendly Verbs** — verbs rated by how reliably AI can perform them. High-confidence verbs (Summarize, Extract, Classify) are safe starting points.
- **Dependencies** — which tasks feed into which. This determines whether you can parallelize.
- **Recommended Approach** — a brief suggestion for how to implement.

## The complexity ladder

The skill classifies your task into one of four levels:

| Level | What it means | Example |
|-------|---------------|---------|
| **Prompt** | Single input, single output. One atomic task. | "Summarize this document" |
| **Workflow** | Multiple tasks chained together. Each feeds the next. | "Extract themes, then draft a post, then edit it" |
| **Agentic Workflow** | Tasks with branching or conditional logic. "If X then Y." | "Classify feedback, then route bugs to Engineering and feature requests to Product" |
| **Agent** | Open-ended goals requiring planning, memory, and tool use. | "Manage customer feedback triage across all channels" |

The complexity level tells you what to build:

- **Prompt** → A single skill or even just a well-crafted prompt
- **Workflow** → Multiple skills chained together, or one skill with sequential steps
- **Agentic Workflow** → Skills with routing logic and conditional paths
- **Agent** → An orchestrating system with state, tools, and decision-making

## GenAI-friendly verbs

Not all verbs are equally suited to AI. The skill flags them by confidence:

| Confidence | Verbs | Notes |
|------------|-------|-------|
| **High** | Summarize, Extract, Classify, Rewrite, Translate, Generate, Format | Reliably handled by LLMs today |
| **Medium** | Compare, Analyze, Explain, Convert, Merge, Split | Good results with clear instructions |
| **Lower** | Decide, Evaluate, Create (original), Research (web) | May need human oversight or tool access |

If your decomposition is full of high-confidence verbs, automation is straightforward. If it's heavy on lower-confidence verbs, plan for human-in-the-loop checkpoints.

## Workflow patterns

When the skill identifies multiple tasks, they connect in one of these patterns:

### Linear chain

```
Task 1 → Task 2 → Task 3 → Output
```

Each step transforms the previous output. Example: Extract → Generate → Edit.

### Fan-out / fan-in

```
         ┌→ Task 2a ─┐
Task 1 ──┼→ Task 2b ──┼→ Task 3 → Output
         └→ Task 2c ─┘
```

Process multiple items in parallel, then merge. Example: collect from email, Slack, and Jira in parallel, then merge into one report.

### Conditional branch

```
                    ┌→ Task 2a → Output A
Task 1 (Classify) ──┤
                    └→ Task 2b → Output B
```

Route to different tasks based on a classification. Example: classify feedback, then route bugs vs. feature requests to different teams.

### Loop with condition

```
Task 1 → Task 2 (Check) ──┬→ Done
              ↑            │
              └────────────┘ (Not done)
```

Repeat until a quality threshold is met. Example: generate draft → evaluate → regenerate if score is too low.

### Human-in-the-loop

```
Task 1 → [Human Review] → Task 2 → Output
```

Pause for human approval before continuing. Use this for high-stakes outputs or when building trust in a new system.

Real workflows often combine patterns. A customer feedback system might use fan-out (collection), conditional branching (routing), a quality loop (draft review), and human-in-the-loop (for critical responses) all at once.

## Generating skill templates

After getting a decomposition, you can ask the skill to generate SKILL.md templates for any of the atomic tasks:

```
Generate skill templates for the tasks in this decomposition
```

Each template follows this structure:

```yaml
---
name: extract-meeting-notes
description: >
  Analyst that extracts key themes from meeting notes to produce structured highlights.
  Use when: user provides meeting notes and needs key takeaways.
---

# Extract Meeting Notes

## Input
Meeting notes (text, document, or transcript)

## Process
1. Identify key decisions made
2. Extract action items with owners
3. Note unresolved questions
4. Pull notable quotes

## Output
Structured list of themes, decisions, and quotes.
Done-when: all meetings covered, themes identified.

## Example
Input: 45-minute product sync transcript
Output: 5 themes, 3 decisions, 2 open questions, 4 action items
```

You can save these as real skills in `~/.claude/skills/` and they'll work immediately.

## End-to-end example: from decomposition to working skills

This section walks through a complete cycle — decomposing a task, generating skill templates, installing them, and running them as a chained workflow.

### Step 1: Decompose the task

```
/workflow-decomposer "Create a blog post from a collection of research notes"
```

The skill identifies the work product (a published blog post), decomposes it into 4 atomic tasks, and classifies it as a **Workflow** (linear chain):

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Analyst | Extract | Key themes, arguments, evidence | User-provided research notes | Structured outline: thesis, supporting points, key quotes | All notes reviewed; no major theme missed |
| 2 | Writer | Generate | Draft blog post | Task 1 output | Full draft (~800–1200 words) with intro, body, conclusion | Coherent narrative; all themes addressed |
| 3 | Editor | Rewrite | Draft for tone and clarity | Task 2 output + style brief | Polished draft matching target voice | Consistent tone; no unexplained jargon |
| 4 | Editor | Format | Final post for publication | Task 3 output | Post with headings, meta description, title options | Ready for CMS; SEO metadata present |

All four verbs — Extract, Generate, Rewrite, Format — are high-confidence GenAI verbs. This is a strong candidate for full automation.

### Step 2: Generate and install skill templates

Asking "generate skill templates for each task" produces four SKILL.md files. Here are the installed skills:

#### `extract-research-themes`

```yaml
---
name: extract-research-themes
description: >
  Analyst that extracts key themes, arguments, and evidence from research notes
  to produce a structured outline. Use when: user provides research notes, reading
  notes, or source material and needs them distilled into an organized structure
  before writing.
---

# Extract Research Themes

## Input
Collection of research notes (text files, documents, or pasted content).

## Process
1. Read all notes end-to-end, identifying recurring themes
2. Extract the core thesis or argument emerging from the material
3. Group supporting points by theme
4. Pull key quotes, statistics, and evidence worth citing
5. Flag contradictions or gaps in the research
6. Order themes by narrative strength

## Output
Structured outline with:
- Thesis statement (1 sentence)
- 3–5 themed sections, each with supporting points and evidence
- Key quotes with attribution
- Noted gaps or open questions

Done-when: all notes reviewed, no major theme missed, redundancies removed.

## Example
Input: 12 pages of notes on remote work productivity studies
Output: Thesis ("Remote work increases output but fragments collaboration"),
4 themes (productivity metrics, communication patterns, employee satisfaction,
management challenges), 6 key quotes, 2 gaps flagged
```

#### `generate-blog-draft`

```yaml
---
name: generate-blog-draft
description: >
  Writer that generates a full blog post draft from a structured outline.
  Use when: user has a completed outline or structured notes and needs a
  first draft written as a cohesive narrative.
---

# Generate Blog Draft

## Input
Structured outline with thesis, themed sections, supporting evidence, and key quotes.

## Process
1. Open with a hook that introduces the thesis without stating it flatly
2. Build each body section from the outline's themed groups
3. Weave in key quotes and evidence as supporting material
4. Create transitions between sections that maintain narrative flow
5. Write a conclusion that reinforces the thesis and offers a takeaway
6. Target 800–1200 words unless otherwise specified

## Output
Full blog post draft with:
- Working title
- Introduction with hook
- Body sections with subheadings
- Integrated quotes and evidence
- Conclusion with takeaway

Done-when: coherent narrative, all outline themes addressed, clear thesis statement present.

## Example
Input: Outline with thesis on remote work + 4 themed sections
Output: 950-word draft titled "The Remote Work Paradox: More Output, Less Connection"
with intro, 4 body sections, and a conclusion
```

#### `rewrite-for-style`

```yaml
---
name: rewrite-for-style
description: >
  Editor that rewrites a draft to match a target voice, tone, and reading level.
  Use when: user has a complete draft that needs polishing for a specific audience
  or publication style. Pairs well with a style guide reference file.
---

# Rewrite for Style

## Input
- Complete draft (from generate-blog-draft or similar)
- Style/audience brief or style guide reference (optional but recommended)

## Process
1. Read the draft fully before making changes
2. Assess current tone, reading level, and voice
3. Compare against target style (from brief or reference)
4. Rewrite for consistent tone throughout
5. Simplify jargon — explain or replace technical terms for the target audience
6. Tighten sentences: remove filler, passive voice, and hedging language
7. Verify all claims and quotes survived the rewrite intact

## Output
Polished draft matching target voice and reading level, with:
- Consistent tone throughout
- No unexplained jargon
- Tighter, more direct prose
- All original evidence and quotes preserved

Done-when: passes editorial checklist, consistent tone, no jargon left unexplained, factual content intact.

## Example
Input: 950-word draft in academic tone + brief saying "conversational, Flesch-Kincaid grade 8"
Output: 900-word rewrite in conversational voice, grade 8 reading level, same structure and evidence
```

#### `format-blog-post`

```yaml
---
name: format-blog-post
description: >
  Editor that formats a polished draft for publication, adding headings, meta
  description, and structural elements. Use when: user has a final draft ready
  to publish and needs it formatted for a specific platform or CMS.
---

# Format Blog Post

## Input
- Polished draft (from rewrite-for-style or similar)
- Post template or platform requirements (optional)

## Process
1. Add or refine subheadings to break text every 200–300 words
2. Identify 1–2 sentences suitable as pull quotes or highlights
3. Write a meta description (under 160 characters) for SEO
4. Generate 2–3 title options (varying in style: direct, question, provocative)
5. Add a suggested slug based on the primary title
6. Format for target platform (Markdown, HTML, or CMS-specific)

## Output
Publication-ready post with:
- Final title + 2 alternatives
- URL slug
- Meta description
- Formatted body with headings and pull quotes
- Platform-appropriate markup

Done-when: ready to paste into CMS, SEO metadata present, headings break up text at regular intervals.

## Example
Input: 900-word polished draft on remote work
Output: Markdown-formatted post with title "The Remote Work Paradox",
slug "remote-work-paradox", meta description, 4 subheadings, 1 pull quote
```

### Step 3: Run them as a linear chain workflow

These four skills form a linear chain — each task's output becomes the next task's input:

```
/extract-research-themes  →  /generate-blog-draft  →  /rewrite-for-style  →  /format-blog-post
```

In practice, you run them one at a time in a Claude Code session:

1. **Provide your research notes** and invoke `/extract-research-themes`. Review the outline it produces.
2. **Feed the outline** to `/generate-blog-draft`. Review the first draft.
3. **Pass the draft** (along with any style preferences) to `/rewrite-for-style`. Review the polished version.
4. **Send the polished draft** to `/format-blog-post`. You get a publication-ready post.

Each step is a natural checkpoint where you can review, adjust, or redirect before continuing. That's the human-in-the-loop pattern built into the workflow by default.

### Why this matters

Notice what happened: a single natural-language task description ("create a blog post from research notes") became four installed, reusable skills. Each skill:

- **Works standalone** — `/rewrite-for-style` is useful for any draft, not just blog posts
- **Has clear boundaries** — defined inputs, outputs, and done-when criteria
- **Chains naturally** — the output format of each skill matches the input expectations of the next
- **Can be swapped** — if you prefer a different extraction approach, replace just that one skill

This is the core value of the decomposition: turning vague work into composable, testable, reusable pieces.

## Worked examples

### Simple: single prompt

**Input**: "Summarize this document into key takeaways"

**Result**: 1 task, Prompt-level complexity.

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Research Assistant | Summarize | Document | User-provided file | Bulleted key takeaways | All main points captured |

No workflow needed. Just a well-structured prompt.

### Medium: workflow

**Input**: "Create a weekly status report from Jira tickets and Slack messages"

**Result**: 4 tasks, Workflow-level with fan-out/fan-in.

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Data Collector | Extract | Jira ticket updates | Jira API / export | Structured ticket list | All tickets this week captured |
| 2 | Data Collector | Extract | Slack messages | Slack channel history | Decisions, blockers, highlights | All channels covered |
| 3 | Analyst | Merge | Jira + Slack extracts | Tasks 1 & 2 | Unified narrative by theme | No duplicates, all sources present |
| 4 | Writer | Format | Merged narrative | Task 3 + template | Formatted report | Matches template, all sections filled |

Tasks 1 and 2 run in parallel, Task 3 merges both, Task 4 formats the final output.

### Complex: agent

**Input**: "Manage customer feedback triage from multiple channels"

**Result**: 8 tasks, Agent-level with multiple patterns.

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1a | Data Collector | Extract | Customer emails | Email API | Structured records | All emails captured |
| 1b | Data Collector | Extract | Social media mentions | Social API | Structured records | Mentions captured, spam filtered |
| 1c | Data Collector | Extract | Support tickets | Helpdesk API | Structured records | All tickets captured |
| 2 | Analyst | Merge | All channel feedback | Tasks 1a-1c | Unified feedback log | Common schema, no duplicates |
| 3 | Classifier | Classify | Each feedback item | Task 2 | Urgency + topic + sentiment | Every item labeled |
| 4 | Router | Route | Classified items | Task 3 + rules | Team assignments | All items assigned |
| 5 | Writer | Generate | Response drafts | Task 3 + templates | Draft replies | Drafts for matching items |
| 6 | Reviewer | Evaluate | Draft responses | Task 5 | Approved/needs-edit | Critical drafts reviewed |
| 7 | Analyst | Analyze | Week's feedback | Task 3 (accumulated) | Trends and insights | Compared to prior week |
| 8 | Writer | Format | Trends analysis | Task 7 + template | Executive report | Under 2 pages |

This combines fan-out (1a/1b/1c), fan-in (2), conditional branching (4), quality loop (5→6), and requires persistent state (7). That's Agent territory.

## Tips

- **Start with the work product.** What actually gets shipped? Work backwards from there.
- **Watch the verbs.** If you're using mostly high-confidence GenAI verbs, you're in good shape for automation.
- **Respect the complexity ladder.** Don't build an Agent when a Workflow will do. Don't build a Workflow when a single Prompt will do.
- **Source matters.** Tasks that need API access or external tools push the complexity up. If the user pastes in data manually, it stays simpler.
- **Done-when is your guardrail.** Vague completion criteria lead to vague outputs. Be specific.
- **Ask for skill templates.** Once you have a decomposition you like, generating SKILL.md files turns the analysis into something you can actually run.

## Skill file structure

```
~/.claude/skills/workflow-decomposer/
├── SKILL.md                          # Core skill instructions
├── tutorial.md                       # Quick-reference tutorial
└── references/
    └── workflow-patterns.md          # Detailed workflow pattern reference
```
