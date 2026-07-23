# Top 10 - Movie Seat Booking - CS SS-focused

Top 10 orchestration combinations for the **Movie Seat Booking** use case, ranked by **CS SS-focused** (0.8 SS / 0.2 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS SS-focused | CS CR-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.8511 | 0.9060 | 0.8786 | 0.8621 | 0.8951 | 0.0475 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.8183 | 0.9154 | 0.8669 | 0.8377 | 0.8960 | 0.0386 |
| 3 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.9105 | 0.8604 | 0.8304 | 0.8905 | 0.0279 |
| 4 | crewai | single_chat | qwen3-30b | Advanced | 0.8078 | 0.8725 | 0.8402 | 0.8207 | 0.8596 | 0.0289 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7954 | 0.9169 | 0.8562 | 0.8197 | 0.8926 | 0.0231 |
| 6 | langgraph | single_chat | qwen3-30b | Advanced | 0.7475 | 0.9391 | 0.8433 | 0.7858 | 0.9008 | 0.0487 |
| 7 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7517 | 0.8957 | 0.8237 | 0.7805 | 0.8669 | 0.0266 |
| 8 | crewai | orchestrator | qwen2.5-72b | Advanced | 0.7877 | 0.7044 | 0.7461 | 0.7711 | 0.7210 | 0.0344 |
| 9 | langgraph | blackboard | qwen3-30b | Advanced | 0.7264 | 0.9421 | 0.8343 | 0.7695 | 0.8990 | 0.0481 |
| 10 | langgraph | sequential | qwen3-30b | Advanced | 0.7243 | 0.9449 | 0.8346 | 0.7684 | 0.9008 | 0.0435 |
