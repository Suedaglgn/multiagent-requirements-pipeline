# Top 10 - Movie Seat Booking - CS balanced (equal weights)

Top 10 orchestration combinations for the **Movie Seat Booking** use case, ranked by **CS balanced (equal weights)** (0.5 SS / 0.5 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS SS-focused | CS CR-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | sequential | qwen3-30b | Advanced | 0.8511 | 0.9060 | 0.8786 | 0.8621 | 0.8951 | 0.0434 |
| 2 | crewai | orchestrator | qwen3-30b | Advanced | 0.8183 | 0.9154 | 0.8669 | 0.8377 | 0.8960 | 0.0392 |
| 3 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.9105 | 0.8604 | 0.8304 | 0.8905 | 0.0207 |
| 4 | crewai | blackboard | qwen3-30b | Advanced | 0.7954 | 0.9169 | 0.8562 | 0.8197 | 0.8926 | 0.0265 |
| 5 | langgraph | single_chat | qwen3-30b | Advanced | 0.7475 | 0.9391 | 0.8433 | 0.7858 | 0.9008 | 0.0269 |
| 6 | crewai | single_chat | qwen3-30b | Advanced | 0.8078 | 0.8725 | 0.8402 | 0.8207 | 0.8596 | 0.0179 |
| 7 | langgraph | sequential | qwen3-30b | Advanced | 0.7243 | 0.9449 | 0.8346 | 0.7684 | 0.9008 | 0.0249 |
| 8 | langgraph | blackboard | qwen3-30b | Advanced | 0.7264 | 0.9421 | 0.8343 | 0.7695 | 0.8990 | 0.0305 |
| 9 | langgraph | orchestrator | qwen3-30b | Advanced | 0.7181 | 0.9426 | 0.8303 | 0.7630 | 0.8977 | 0.0191 |
| 10 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.7517 | 0.8957 | 0.8237 | 0.7805 | 0.8669 | 0.0319 |
