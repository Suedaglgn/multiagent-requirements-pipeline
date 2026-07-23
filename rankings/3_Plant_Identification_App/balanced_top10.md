# Top 10 - Plant Identification App - CS balanced (equal weights)

Top 10 orchestration combinations for the **Plant Identification App** use case, ranked by **CS balanced (equal weights)** (0.5 SS / 0.5 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.7722 | 0.8868 | 0.8295 | 0.7951 | 0.8638 | 0.0396 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.7810 | 0.8774 | 0.8292 | 0.8003 | 0.8582 | 0.0604 |
| 3 | crewai | blackboard | qwen3-30b | Advanced | 0.7530 | 0.8917 | 0.8224 | 0.7807 | 0.8640 | 0.0567 |
| 4 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.7629 | 0.8691 | 0.8160 | 0.7842 | 0.8479 | 0.0450 |
| 5 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6979 | 0.9225 | 0.8102 | 0.7428 | 0.8776 | 0.0210 |
| 6 | langgraph | sequential | qwen3-30b | Advanced | 0.6782 | 0.9267 | 0.8024 | 0.7279 | 0.8770 | 0.0170 |
| 7 | langgraph | single_chat | qwen3-30b | Advanced | 0.6797 | 0.9225 | 0.8011 | 0.7283 | 0.8740 | 0.0189 |
| 8 | crewai | single_chat | qwen3-30b | Advanced | 0.8097 | 0.7876 | 0.7986 | 0.8053 | 0.7920 | 0.0494 |
| 9 | langgraph | blackboard | qwen3-30b | Advanced | 0.6655 | 0.9250 | 0.7953 | 0.7174 | 0.8731 | 0.0394 |
| 10 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7202 | 0.8594 | 0.7898 | 0.7481 | 0.8315 | 0.0358 |
