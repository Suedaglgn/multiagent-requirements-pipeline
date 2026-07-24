# Top 10 - AI Study Assistant - CS balanced (equal weights)

Top 10 orchestration combinations for the **AI Study Assistant** use case, ranked by **CS balanced (equal weights)** (0.5 SS / 0.5 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS balanced (min/median/max/std)` columns describe the **CS balanced (equal weights)** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS balanced (min) | CS balanced (median) | CS balanced (max) | CS balanced (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.7394 | 0.8957 | 0.8176 | 0.7707 | 0.8645 | 0.7850 | 0.8233 | 0.8439 | 0.0223 |
| 2 | crewai | blackboard | qwen3-30b | Advanced | 0.7332 | 0.8917 | 0.8124 | 0.7649 | 0.8600 | 0.7665 | 0.8117 | 0.8609 | 0.0357 |
| 3 | crewai | single_chat | qwen3-30b | Advanced | 0.7888 | 0.8147 | 0.8017 | 0.7940 | 0.8095 | 0.7682 | 0.8133 | 0.8264 | 0.0257 |
| 4 | crewai | orchestrator | qwen3-30b | Advanced | 0.7020 | 0.8948 | 0.7984 | 0.7406 | 0.8562 | 0.7601 | 0.7921 | 0.8593 | 0.0372 |
| 5 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.6704 | 0.9000 | 0.7852 | 0.7163 | 0.8540 | 0.7251 | 0.8072 | 0.8412 | 0.0509 |
| 6 | langgraph | orchestrator | qwen3-30b | Advanced | 0.6367 | 0.9314 | 0.7841 | 0.6957 | 0.8725 | 0.7549 | 0.7795 | 0.8273 | 0.0307 |
| 7 | langgraph | single_chat | qwen3-30b | Advanced | 0.6351 | 0.9267 | 0.7809 | 0.6934 | 0.8684 | 0.7235 | 0.7951 | 0.8065 | 0.0349 |
| 8 | langgraph | sequential | qwen3-30b | Advanced | 0.6235 | 0.9317 | 0.7776 | 0.6851 | 0.8701 | 0.7507 | 0.7740 | 0.8104 | 0.0244 |
| 9 | langgraph | blackboard | qwen3-30b | Advanced | 0.6267 | 0.9278 | 0.7772 | 0.6869 | 0.8675 | 0.7516 | 0.7728 | 0.8109 | 0.0217 |
| 10 | langgraph | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.6627 | 0.8888 | 0.7758 | 0.7079 | 0.8436 | 0.7149 | 0.7787 | 0.8133 | 0.0411 |
