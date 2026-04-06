---
name: experiment-log
description: >
  Log experiment with full configuration, results, and observations.
  Track hyperparameters, compute usage, and best checkpoints.
  Triggers: "log experiment", "track run", "record experiment",
  "experiment results", "save run", "log training run".
---

# Experiment Log Skill

Structured experiment tracking for reproducible research.

## Capabilities

1. **Config Logging** — Full hyperparameter tracking
2. **Result Recording** — Metrics, benchmarks, observations
3. **Compute Tracking** — GPU hours, cost, efficiency
4. **Checkpoint Management** — Best model tracking
5. **Ablation Design** — Structured ablation studies

## Usage

```
/experiment-log
/experiment-log --project project-alpha
/experiment-log --template ablation
```

## Experiment Note Format

Creates structured note with:
- Experiment ID and project
- Full configuration (model, training, data)
- Results (metrics, comparisons)
- Compute resources
- Observations and next steps
- Links to checkpoints and logs
