# Rankings: Top-10 Leaderboards

Per-use-case leaderboards of the best-performing pipeline configurations.

## What is ranked

Each experiment configuration is a **combination** of
`(orchestration method, workflow, llm, prompt_level)`:

- **orchestration method** - the agent framework (`crewai`, `langgraph`).
- **workflow** - the agent topology (`blackboard`, `orchestrator`, `sequential`, `single_chat`).
- **llm** - the model (`qwen3-30b`, `qwen2.5-72b`, `Nvidia-Nemotron3-Nano`).
- **prompt_level** - the prompt-engineering level (`Basic`, `Enhanced`, `Advanced`).

Every combination was executed **5 times** on all use cases; each score below is the **average over
those 5 runs** for a single use case.

## Metrics and weighting schemes

Configurations are scored with a **Composite Score (CS)** that blends two normalized sub-metrics:

- **SS** - Semantic Similarity to the ground truth (ROUGE-1, ROUGE-L, METEOR, BERTScore, BLEU-4).
- **CR** - Cost Rate (latency and token usage, inverted so cheaper/faster scores higher).

Three CS weighting schemes each get their own leaderboard file:

| File            | Weighting scheme | Weights (SS / CR) |
| --------------- | ---------------- | ----------------- |
| `balanced_*`    | CS equal         | 0.5 / 0.5         |
| `SS_focused_*`  | CS SS-weighted   | 0.8 / 0.2         |
| `CR_focused_*`  | CS CR-weighted   | 0.2 / 0.8         |

## Layout

```
rankings/
  <use_case>/
    balanced_top10.md      # top 10 by CS equal
    SS_focused_top10.md    # top 10 by CS SS-weighted
    CR_focused_top10.md    # top 10 by CS CR-weighted
```

Use-case folder names match those under `examples/`:

| Folder                         | Use case                 |
| ------------------------------ | ------------------------ |
| `1_HR_and_Payroll_Mobile_App/` | HR & payroll mobile app  |
| `2_AI_Study_Assistant/`        | AI study assistant       |
| `3_Plant_Identification_App/`  | Plant identification app |
| `4_Movie_Seat_Booking/`        | Movie seat booking app   |

Each file lists the top 10 combinations for one use case under one scheme (rank 1 = best). The
top-3 combinations of each list are exactly the ones whose generated documents are provided as the
`*_first.md` / `*_second.md` / `*_third.md` selections in
[../examples/README.md](../examples/README.md) (`examples/<use_case>/{RA,DE}/`).
