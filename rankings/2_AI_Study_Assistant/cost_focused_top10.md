# Top 10 - AI Study Assistant - CS cost-focused

Top 10 orchestration combinations for the **AI Study Assistant** use case, ranked by **CS cost-focused** (0.2 SS / 0.8 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS cost-focused (min/median/max/std)` columns describe the **CS cost-focused** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS cost-focused (min) | CS cost-focused (median) | CS cost-focused (max) | CS cost-focused (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6367 | 0.9314 | 0.7841 | 0.6957 | 0.8725 | 0.8560 | 0.8697 | 0.8932 | 0.0141 |
| 2 | langgraph | sequential | qwen3-30b | Advanced | 0.6235 | 0.9317 | 0.7776 | 0.6851 | 0.8701 | 0.8443 | 0.8680 | 0.8858 | 0.0170 |
| 3 | langgraph | single_chat | qwen3-30b | Advanced | 0.6351 | 0.9267 | 0.7809 | 0.6934 | 0.8684 | 0.8456 | 0.8769 | 0.8839 | 0.0176 |
| 4 | langgraph | blackboard | qwen3-30b | Advanced | 0.6267 | 0.9278 | 0.7772 | 0.6869 | 0.8675 | 0.8523 | 0.8677 | 0.8837 | 0.0115 |
| 5 | crewai | sequential | qwen3-30b | Advanced | 0.7394 | 0.8957 | 0.8176 | 0.7707 | 0.8645 | 0.8338 | 0.8651 | 0.8850 | 0.0212 |
| 6 | crewai | blackboard | qwen3-30b | Advanced | 0.7332 | 0.8917 | 0.8124 | 0.7649 | 0.8600 | 0.7906 | 0.8802 | 0.8929 | 0.0425 |
| 7 | crewai | orchestrator | qwen3-30b | Advanced | 0.7020 | 0.8948 | 0.7984 | 0.7406 | 0.8562 | 0.7880 | 0.8693 | 0.8903 | 0.0400 |
| 8 | langgraph | orchestrator | qwen3-30b | Enhanced | 0.5522 | 0.9321 | 0.7422 | 0.6282 | 0.8561 | 0.8227 | 0.8584 | 0.8786 | 0.0208 |
| 9 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.6704 | 0.9000 | 0.7852 | 0.7163 | 0.8540 | 0.8158 | 0.8589 | 0.8788 | 0.0236 |
| 10 | langgraph | single_chat | qwen3-30b | Enhanced | 0.5350 | 0.9313 | 0.7332 | 0.6143 | 0.8520 | 0.8323 | 0.8468 | 0.8797 | 0.0187 |
