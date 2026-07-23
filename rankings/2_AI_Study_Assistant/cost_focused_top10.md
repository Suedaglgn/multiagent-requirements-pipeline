# Top 10 - AI Study Assistant - CS cost-focused

Top 10 orchestration combinations for the **AI Study Assistant** use case, ranked by **CS cost-focused** (0.2 SS / 0.8 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6367 | 0.9314 | 0.7841 | 0.6957 | 0.8725 | 0.0141 |
| 2 | langgraph | sequential | qwen3-30b | Advanced | 0.6235 | 0.9317 | 0.7776 | 0.6851 | 0.8701 | 0.0170 |
| 3 | langgraph | single_chat | qwen3-30b | Advanced | 0.6351 | 0.9267 | 0.7809 | 0.6934 | 0.8684 | 0.0176 |
| 4 | langgraph | blackboard | qwen3-30b | Advanced | 0.6267 | 0.9278 | 0.7772 | 0.6869 | 0.8675 | 0.0115 |
| 5 | crewai | sequential | qwen3-30b | Advanced | 0.7394 | 0.8957 | 0.8176 | 0.7707 | 0.8645 | 0.0212 |
| 6 | crewai | blackboard | qwen3-30b | Advanced | 0.7332 | 0.8917 | 0.8124 | 0.7649 | 0.8600 | 0.0425 |
| 7 | crewai | orchestrator | qwen3-30b | Advanced | 0.7020 | 0.8948 | 0.7984 | 0.7406 | 0.8562 | 0.0400 |
| 8 | langgraph | orchestrator | qwen3-30b | Enhanced | 0.5522 | 0.9321 | 0.7422 | 0.6282 | 0.8561 | 0.0208 |
| 9 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.6704 | 0.9000 | 0.7852 | 0.7163 | 0.8540 | 0.0236 |
| 10 | langgraph | single_chat | qwen3-30b | Enhanced | 0.5350 | 0.9313 | 0.7332 | 0.6143 | 0.8520 | 0.0187 |
