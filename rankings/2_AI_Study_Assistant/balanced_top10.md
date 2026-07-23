# Top 10 - AI Study Assistant - CS balanced (equal weights)

Top 10 orchestration combinations for the **AI Study Assistant** use case, ranked by **CS balanced (equal weights)** (0.5 SS / 0.5 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.7394 | 0.8957 | 0.8176 | 0.7707 | 0.8645 | 0.0223 |
| 2 | crewai | blackboard | qwen3-30b | Advanced | 0.7332 | 0.8917 | 0.8124 | 0.7649 | 0.8600 | 0.0357 |
| 3 | crewai | single_chat | qwen3-30b | Advanced | 0.7888 | 0.8147 | 0.8017 | 0.7940 | 0.8095 | 0.0257 |
| 4 | crewai | orchestrator | qwen3-30b | Advanced | 0.7020 | 0.8948 | 0.7984 | 0.7406 | 0.8562 | 0.0372 |
| 5 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.6704 | 0.9000 | 0.7852 | 0.7163 | 0.8540 | 0.0509 |
| 6 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6367 | 0.9314 | 0.7841 | 0.6957 | 0.8725 | 0.0307 |
| 7 | langgraph | single_chat | qwen3-30b | Advanced | 0.6351 | 0.9267 | 0.7809 | 0.6934 | 0.8684 | 0.0349 |
| 8 | langgraph | sequential | qwen3-30b | Advanced | 0.6235 | 0.9317 | 0.7776 | 0.6851 | 0.8701 | 0.0244 |
| 9 | langgraph | blackboard | qwen3-30b | Advanced | 0.6267 | 0.9278 | 0.7772 | 0.6869 | 0.8675 | 0.0217 |
| 10 | langgraph | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.6627 | 0.8888 | 0.7758 | 0.7079 | 0.8436 | 0.0411 |
