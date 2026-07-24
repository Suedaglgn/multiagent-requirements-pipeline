# Top 10 - Plant Identification App - CS quality-focused

Top 10 orchestration combinations for the **Plant Identification App** use case, ranked by **CS quality-focused** (0.8 SS / 0.2 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS quality-focused (min/median/max/std)` columns describe the **CS quality-focused** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS quality-focused (min) | CS quality-focused (median) | CS quality-focused (max) | CS quality-focused (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | single_chat | qwen3-30b | Advanced | 0.8097 | 0.7876 | 0.7986 | 0.8053 | 0.7920 | 0.7084 | 0.8204 | 0.8713 | 0.0682 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.7810 | 0.8774 | 0.8292 | 0.8003 | 0.8582 | 0.6824 | 0.8280 | 0.8527 | 0.0682 |
| 3 | crewai | sequential | qwen3-30b | Advanced | 0.7722 | 0.8868 | 0.8295 | 0.7951 | 0.8638 | 0.7468 | 0.7818 | 0.8623 | 0.0490 |
| 4 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.7629 | 0.8691 | 0.8160 | 0.7842 | 0.8479 | 0.7108 | 0.7770 | 0.8472 | 0.0619 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7530 | 0.8917 | 0.8224 | 0.7807 | 0.8640 | 0.6680 | 0.7661 | 0.8958 | 0.0826 |
| 6 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7202 | 0.8594 | 0.7898 | 0.7481 | 0.8315 | 0.6748 | 0.7552 | 0.8107 | 0.0491 |
| 7 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6979 | 0.9225 | 0.8102 | 0.7428 | 0.8776 | 0.6909 | 0.7413 | 0.7941 | 0.0404 |
| 8 | langgraph | single_chat | qwen3-30b | Advanced | 0.6797 | 0.9225 | 0.8011 | 0.7283 | 0.8740 | 0.6584 | 0.7295 | 0.7859 | 0.0458 |
| 9 | langgraph | sequential | qwen3-30b | Advanced | 0.6782 | 0.9267 | 0.8024 | 0.7279 | 0.8770 | 0.6807 | 0.7420 | 0.7504 | 0.0292 |
| 10 | crewai | sequential | Nvidia-Nemotron3-Nano | Advanced | 0.6888 | 0.8568 | 0.7728 | 0.7224 | 0.8232 | 0.6072 | 0.7202 | 0.8166 | 0.0837 |
