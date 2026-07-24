# Top 10 - Plant Identification App - CS cost-focused

Top 10 orchestration combinations for the **Plant Identification App** use case, ranked by **CS cost-focused** (0.2 SS / 0.8 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS cost-focused (min/median/max/std)` columns describe the **CS cost-focused** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS cost-focused (min) | CS cost-focused (median) | CS cost-focused (max) | CS cost-focused (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6979 | 0.9225 | 0.8102 | 0.7428 | 0.8776 | 0.8555 | 0.8798 | 0.8919 | 0.0134 |
| 2 | langgraph | sequential | qwen3-30b | Advanced | 0.6782 | 0.9267 | 0.8024 | 0.7279 | 0.8770 | 0.8548 | 0.8779 | 0.8937 | 0.0150 |
| 3 | langgraph | single_chat | qwen3-30b | Advanced | 0.6797 | 0.9225 | 0.8011 | 0.7283 | 0.8740 | 0.8533 | 0.8780 | 0.8807 | 0.0117 |
| 4 | langgraph | blackboard | qwen3-30b | Advanced | 0.6655 | 0.9250 | 0.7953 | 0.7174 | 0.8731 | 0.8393 | 0.8795 | 0.8875 | 0.0195 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7530 | 0.8917 | 0.8224 | 0.7807 | 0.8640 | 0.7880 | 0.8851 | 0.9051 | 0.0466 |
| 6 | crewai | sequential | qwen3-30b | Advanced | 0.7722 | 0.8868 | 0.8295 | 0.7951 | 0.8638 | 0.8215 | 0.8800 | 0.8975 | 0.0326 |
| 7 | crewai | orchestrator | qwen3-30b | Advanced | 0.7810 | 0.8774 | 0.8292 | 0.8003 | 0.8582 | 0.7698 | 0.8872 | 0.8962 | 0.0531 |
| 8 | langgraph | orchestrator | qwen3-30b | Enhanced | 0.5507 | 0.9324 | 0.7415 | 0.6271 | 0.8560 | 0.8237 | 0.8585 | 0.8738 | 0.0194 |
| 9 | langgraph | sequential | qwen3-30b | Enhanced | 0.5782 | 0.9239 | 0.7511 | 0.6473 | 0.8548 | 0.8024 | 0.8625 | 0.8769 | 0.0302 |
| 10 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Basic | 0.3780 | 0.9675 | 0.6728 | 0.4959 | 0.8496 | 0.8356 | 0.8487 | 0.8642 | 0.0102 |
