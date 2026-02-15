# Workflow Patterns

Common patterns for combining atomic tasks into workflows.

## Linear Chain

Tasks execute in sequence, each feeding the next.

```
Task 1 → Task 2 → Task 3 → Output
```

**Example**: Extract → Generate → Edit

**When to use**: Simple pipelines where each step transforms the previous output.

**Skill design**: Can be one skill with multiple steps, or separate skills chained.

---

## Fan-Out / Fan-In

One task spawns multiple parallel tasks, results merge.

```
         ┌→ Task 2a ─┐
Task 1 ──┼→ Task 2b ──┼→ Task 3 → Output
         └→ Task 2c ─┘
```

**Example**: Split document by section → Summarize each → Merge summaries

**When to use**: Processing multiple items independently then combining.

**Skill design**: Usually one orchestrating skill that handles the parallelism.

---

## Conditional Branch

Route to different tasks based on classification.

```
                    ┌→ Task 2a → Output A
Task 1 (Classify) ──┤
                    └→ Task 2b → Output B
```

**Example**: Classify email → Route to "Reply" or "Archive" skill

**When to use**: Different processing paths for different input types.

**Skill design**: Router skill that invokes other skills based on classification.

---

## Loop with Condition

Repeat until quality threshold met.

```
Task 1 → Task 2 (Check) ──┬→ Done
              ↑           │
              └───────────┘ (Not done)
```

**Example**: Generate → Evaluate → (if score < threshold) Regenerate

**When to use**: Quality-sensitive outputs that may need iteration.

**Skill design**: Single skill with internal loop, or separate Evaluate skill.

---

## Human-in-the-Loop

Pause for human review before continuing.

```
Task 1 → [Human Review] → Task 2 → Output
```

**Example**: Draft → Human approval → Publish

**When to use**: High-stakes outputs, learning what good looks like.

**Skill design**: Skill outputs draft + prompts for feedback; separate skill continues.

---

## Choosing the Right Pattern

| Situation | Pattern |
|-----------|---------|
| Simple transformation pipeline | Linear Chain |
| Processing batches of similar items | Fan-Out / Fan-In |
| Different handling for different types | Conditional Branch |
| Quality must meet threshold | Loop with Condition |
| Stakes are high, trust is building | Human-in-the-Loop |

## Combining Patterns

Real workflows often combine patterns:

```
                         ┌→ Summarize ─┐
Input → Classify ────────┤              ├→ Human Review → Publish
                         └→ Translate ─┘
```

This combines: Conditional Branch + Fan-Out + Human-in-the-Loop
