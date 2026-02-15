# Workflow Decomposer — Test Log

**Date**: 2026-02-14
**Skill Version**: 1.0 (with Source column enhancement)
**Invocation Method**: `/workflow-decomposer` slash command in Claude Code

---

## Test 1: Simple (Prompt-level)

**Input**: `"Summarize this document into key takeaways"`

**Result**:

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Research Assistant | Summarize | Document | User-provided file/text | Bulleted list of key takeaways | All main points captured; no critical information omitted; each takeaway is self-contained |

- **Complexity**: Prompt (single atomic task)
- **GenAI-Friendly Verbs**: Summarize (high)
- **Assessment**: Correctly identified as a single-prompt task. No unnecessary decomposition.

**Verdict**: PASS

---

## Test 2: Medium (Workflow-level)

**Input**: `"Create a weekly status report from Jira tickets and Slack messages"`

**Result**:

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1 | Data Collector | Extract | Jira ticket updates | Jira API / export | Structured list: ticket ID, title, status, assignee, summary | All tickets touched this week captured |
| 2 | Data Collector | Extract | Slack messages (project channels) | Slack export / channel history | Key decisions, announcements, blockers, highlights | All relevant channels covered; noise filtered |
| 3 | Analyst | Merge | Jira + Slack extracts | Task 1 + Task 2 output | Unified progress narrative grouped by project/theme | No duplicates; all sources represented |
| 4 | Writer | Format | Merged narrative | Task 3 output + template | Formatted report: Accomplishments, In Progress, Blockers, Next Week | Matches template; under 1 page; all sections populated |

- **Complexity**: Workflow (fan-out / fan-in pattern)
- **GenAI-Friendly Verbs**: Extract (high) x2, Merge (medium), Format (high)
- **Dependencies correctly identified**: Tasks 1 & 2 parallel, Task 3 waits for both, Task 4 follows Task 3
- **Pattern correctly identified**: Fan-out / fan-in

**Verdict**: PASS

---

## Test 3: Complex (Agent-level)

**Input**: `"Manage customer feedback triage from multiple channels — collect from email, social media, and support tickets, classify by urgency and topic, route to the right team, draft responses for common issues, and produce a weekly trends report for product leadership"`

**Result**: 8 atomic tasks identified:

| # | Role | Verb | Noun | Source | Output | Done-when |
|---|------|------|------|--------|--------|-----------|
| 1a | Data Collector | Extract | Customer emails | Email API | Structured feedback records | All unprocessed emails captured |
| 1b | Data Collector | Extract | Social media mentions | Social media API | Structured feedback records | All mentions captured; spam filtered |
| 1c | Data Collector | Extract | Support tickets | Helpdesk API | Structured feedback records | All new/updated tickets captured |
| 2 | Analyst | Merge | Feedback from all channels | Tasks 1a-1c | Unified feedback log | No duplicates; common schema |
| 3 | Classifier | Classify | Each feedback item | Task 2 | Urgency + topic + sentiment labels | Every item labeled with confidence scores |
| 4 | Router | Route | Classified items | Task 3 + routing rules | Team/owner assignments | All items assigned; critical flagged |
| 5 | Writer | Generate | Response drafts | Task 3 + templates | Draft replies per item | Drafts for all template-matching items |
| 6 | Reviewer | Evaluate | Draft responses | Task 5 | Approved/needs-edit flags | All critical drafts reviewed |
| 7 | Analyst | Analyze | Week's classified feedback | Task 3 (accumulated) | Trends, sentiment shifts, emerging issues | All data included; compared to prior week |
| 8 | Writer | Format | Trends analysis | Task 7 + template | Weekly executive report | Under 2 pages; actionable recommendations |

- **Complexity**: Agent (correctly identified)
- **GenAI-Friendly Verbs**: Extract (high) x3, Merge (medium), Classify (high), Generate (high), Analyze (medium), Format (high), Evaluate (lower), Route (lower)
- **Patterns identified**: Fan-out/fan-in, conditional branch, loop with condition, human-in-the-loop
- **Dependencies correctly mapped** including parallel paths and state accumulation

**Verdict**: PASS

---

## Summary

| Test | Input Complexity | Expected Level | Detected Level | Tasks Found | Verdict |
|------|-----------------|----------------|----------------|-------------|---------|
| 1 | Simple | Prompt | Prompt | 1 | PASS |
| 2 | Medium | Workflow | Workflow | 4 | PASS |
| 3 | Complex | Agent | Agent | 8 | PASS |

All three tests demonstrate correct decomposition with appropriate:
- Role assignment per task
- GenAI-friendly verb identification with confidence levels
- Source tracking for input provenance
- Done-when completion criteria
- Dependency mapping between tasks
- Workflow pattern recognition
- Complexity level assessment
