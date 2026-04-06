---
name: idea-generation
description: >
  Structured brainstorming session to generate research ideas.
  Combines papers, identifies gaps, suggests experiments.
  Triggers: "generate ideas", "brainstorm", "research ideas",
  "what should we try", "creative session", "idea synthesis",
  "combine papers", "novel approach", "breakthrough ideas".
---

# Idea Generation Skill

Structured creative session for research ideation.

## Session Types

### 1. Paper Combination

```
/idea-generation combine "Paper A" "Paper B"
```

Combines techniques from two papers to generate novel approaches.

### 2. Gap Exploration

```
/idea-generation gaps "mixture of experts"
```

Identifies gaps in a research area and suggests ways to fill them.

### 3. Constraint Challenge

```
/idea-generation constraint "70B model" "single GPU inference"
```

Generates ideas for solving a problem under specific constraints.

### 4. Free Exploration

```
/idea-generation explore --topic "efficiency" --count 10
```

Open-ended idea generation in a research area.

## Output

- 5-10 structured ideas
- Confidence scores
- Implementation sketches
- Resource estimates
- Prioritized by impact/feasibility
