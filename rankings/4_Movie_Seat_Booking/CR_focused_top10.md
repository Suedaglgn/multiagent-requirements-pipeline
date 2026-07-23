# Top 10 - Movie Seat Booking - CS CR-focused

Top 10 orchestration combinations for the **Movie Seat Booking** use case, ranked by **CS CR-focused** (0.2 SS / 0.8 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS SS-focused | CS CR-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | langgraph | sequential | qwen3-30b | Advanced | 0.7243 | 0.9449 | 0.8346 | 0.7684 | 0.9008 | 0.0119 |
| 2 | langgraph | single_chat | qwen3-30b | Advanced | 0.7475 | 0.9391 | 0.8433 | 0.7858 | 0.9008 | 0.0085 |
| 3 | langgraph | blackboard | qwen3-30b | Advanced | 0.7264 | 0.9421 | 0.8343 | 0.7695 | 0.8990 | 0.0163 |
| 4 | langgraph | orchestrator | qwen3-30b | Advanced | 0.7181 | 0.9426 | 0.8303 | 0.7630 | 0.8977 | 0.0083 |
| 5 | crewai | orchestrator | qwen3-30b | Advanced | 0.8183 | 0.9154 | 0.8669 | 0.8377 | 0.8960 | 0.0406 |
| 6 | langgraph | sequential | Nvidia-Nemotron3-Nano | Enhanced | 0.6967 | 0.9449 | 0.8208 | 0.7463 | 0.8952 | 0.0205 |
| 7 | crewai | sequential | qwen3-30b | Advanced | 0.8511 | 0.9060 | 0.8786 | 0.8621 | 0.8951 | 0.0417 |
| 8 | crewai | blackboard | qwen3-30b | Advanced | 0.7954 | 0.9169 | 0.8562 | 0.8197 | 0.8926 | 0.0383 |
| 9 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.9105 | 0.8604 | 0.8304 | 0.8905 | 0.0180 |
| 10 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Basic | 0.5483 | 0.9757 | 0.7620 | 0.6338 | 0.8902 | 0.0083 |
