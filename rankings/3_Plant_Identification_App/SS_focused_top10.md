# Top 10 - Plant Identification App - CS SS-focused

Top 10 orchestration combinations for the **Plant Identification App** use case, ranked by **CS SS-focused** (0.8 SS / 0.2 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS SS-focused | CS CR-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | single_chat | qwen3-30b | Advanced | 0.8097 | 0.7876 | 0.7986 | 0.8053 | 0.7920 | 0.0682 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.7810 | 0.8774 | 0.8292 | 0.8003 | 0.8582 | 0.0682 |
| 3 | crewai | sequential | qwen3-30b | Advanced | 0.7722 | 0.8868 | 0.8295 | 0.7951 | 0.8638 | 0.0490 |
| 4 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.7629 | 0.8691 | 0.8160 | 0.7842 | 0.8479 | 0.0619 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7530 | 0.8917 | 0.8224 | 0.7807 | 0.8640 | 0.0826 |
| 6 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7202 | 0.8594 | 0.7898 | 0.7481 | 0.8315 | 0.0491 |
| 7 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6979 | 0.9225 | 0.8102 | 0.7428 | 0.8776 | 0.0404 |
| 8 | langgraph | single_chat | qwen3-30b | Advanced | 0.6797 | 0.9225 | 0.8011 | 0.7283 | 0.8740 | 0.0458 |
| 9 | langgraph | sequential | qwen3-30b | Advanced | 0.6782 | 0.9267 | 0.8024 | 0.7279 | 0.8770 | 0.0292 |
| 10 | crewai | sequential | Nvidia-Nemotron3-Nano | Advanced | 0.6888 | 0.8568 | 0.7728 | 0.7224 | 0.8232 | 0.0837 |
