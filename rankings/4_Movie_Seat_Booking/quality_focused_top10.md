# Top 10 - Movie Seat Booking - CS quality-focused

Top 10 orchestration combinations for the **Movie Seat Booking** use case, ranked by **CS quality-focused** (0.8 SS / 0.2 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS quality-focused (min/median/max/std)` columns describe the **CS quality-focused** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS quality-focused (min) | CS quality-focused (median) | CS quality-focused (max) | CS quality-focused (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.8511 | 0.9060 | 0.8786 | 0.8621 | 0.8951 | 0.7811 | 0.8817 | 0.8980 | 0.0475 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.8183 | 0.9154 | 0.8669 | 0.8377 | 0.8960 | 0.7750 | 0.8515 | 0.8727 | 0.0386 |
| 3 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.9105 | 0.8604 | 0.8304 | 0.8905 | 0.8119 | 0.8226 | 0.8792 | 0.0279 |
| 4 | crewai | single_chat | qwen3-30b | Advanced | 0.8078 | 0.8725 | 0.8402 | 0.8207 | 0.8596 | 0.7785 | 0.8185 | 0.8562 | 0.0289 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7954 | 0.9169 | 0.8562 | 0.8197 | 0.8926 | 0.7984 | 0.8143 | 0.8576 | 0.0231 |
| 6 | langgraph | single_chat | qwen3-30b | Advanced | 0.7475 | 0.9391 | 0.8433 | 0.7858 | 0.9008 | 0.7310 | 0.7639 | 0.8500 | 0.0487 |
| 7 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7517 | 0.8957 | 0.8237 | 0.7805 | 0.8669 | 0.7537 | 0.7706 | 0.8176 | 0.0266 |
| 8 | crewai | orchestrator | qwen2.5-72b | Advanced | 0.7877 | 0.7044 | 0.7461 | 0.7711 | 0.7210 | 0.7180 | 0.7755 | 0.8044 | 0.0344 |
| 9 | langgraph | blackboard | qwen3-30b | Advanced | 0.7264 | 0.9421 | 0.8343 | 0.7695 | 0.8990 | 0.7090 | 0.7786 | 0.8300 | 0.0481 |
| 10 | langgraph | sequential | qwen3-30b | Advanced | 0.7243 | 0.9449 | 0.8346 | 0.7684 | 0.9008 | 0.7127 | 0.7661 | 0.8235 | 0.0435 |
