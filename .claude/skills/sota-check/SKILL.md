---
name: sota-check
description: >
  Check current state-of-the-art on benchmarks, compare your results,
  and identify competitive gaps. Track progress over time.
  Triggers: "what's sota", "sota check", "are we competitive",
  "benchmark status", "leaderboard", "where do we stand",
  "mmlu results", "humaneval sota".
---

# SoTA Check Skill

Comprehensive benchmark and competitive analysis.

## Capabilities

1. **SoTA Query** — Current best on any benchmark
2. **Your Position** — Where you rank vs competition
3. **Gap Analysis** — How far behind and why
4. **Trend Tracking** — Progress over time
5. **Alert Generation** — When you're no longer competitive

## Usage

```
/sota-check mmlu
/sota-check all
/sota-check humaneval --compare-to openai
/sota-check gsm8k --history 6months
```

## Output Format

```markdown
## SoTA Check: MMLU

### Current Standings
- SoTA: OpenAI o3 — 90.2%
- Your best: Your Model v2 — 72.3%
- Gap: 17.9 percentage points

### Your Trajectory
- 3 months ago: 68.2%
- Current: 72.3%
- Improvement: +4.1%

### Assessment
- Status: Not competitive, but improving
- Time to catch up: ~18 months at current rate
- Recommendation: Accelerate data quality improvements

### Active Experiments
- exp-045: Predicted 74.1% (if successful, closes gap by 1.8pp)
```
