# Top 10 - HR & Payroll Mobile App - CS balanced (equal weights)

Top 10 orchestration combinations for the **HR & Payroll Mobile App** use case, ranked by **CS balanced (equal weights)** (0.5 SS / 0.5 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS SS-focused | CS CR-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8097 | 0.8616 | 0.8356 | 0.8200 | 0.8512 | 0.0289 |
| 2 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.8584 | 0.8344 | 0.8200 | 0.8488 | 0.0229 |
| 3 | crewai | sequential | qwen3-30b | Advanced | 0.7980 | 0.8674 | 0.8327 | 0.8119 | 0.8535 | 0.0537 |
| 4 | crewai | orchestrator | qwen3-30b | Advanced | 0.7522 | 0.8974 | 0.8248 | 0.7812 | 0.8684 | 0.0435 |
| 5 | langgraph | orchestrator | qwen3-30b | Advanced | 0.7261 | 0.9183 | 0.8222 | 0.7646 | 0.8799 | 0.0302 |
| 6 | crewai | blackboard | qwen3-30b | Advanced | 0.7535 | 0.8875 | 0.8205 | 0.7803 | 0.8607 | 0.0291 |
| 7 | langgraph | single_chat | qwen3-30b | Advanced | 0.6560 | 0.9309 | 0.7935 | 0.7110 | 0.8759 | 0.0439 |
| 8 | langgraph | sequential | qwen3-30b | Advanced | 0.6526 | 0.9295 | 0.7911 | 0.7080 | 0.8741 | 0.0110 |
| 9 | crewai | sequential | Nvidia-Nemotron3-Nano | Advanced | 0.6757 | 0.8863 | 0.7810 | 0.7179 | 0.8442 | 0.0447 |
| 10 | langgraph | blackboard | qwen3-30b | Advanced | 0.6222 | 0.9339 | 0.7780 | 0.6845 | 0.8715 | 0.0186 |
