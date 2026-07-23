# Top 10 - HR & Payroll Mobile App - CS cost-focused

Top 10 orchestration combinations for the **HR & Payroll Mobile App** use case, ranked by **CS cost-focused** (0.2 SS / 0.8 CR), averaged over the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | Std dev |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | langgraph | orchestrator | qwen3-30b | Advanced | 0.7261 | 0.9183 | 0.8222 | 0.7646 | 0.8799 | 0.0129 |
| 2 | langgraph | single_chat | qwen3-30b | Advanced | 0.6560 | 0.9309 | 0.7935 | 0.7110 | 0.8759 | 0.0165 |
| 3 | langgraph | sequential | qwen3-30b | Advanced | 0.6526 | 0.9295 | 0.7911 | 0.7080 | 0.8741 | 0.0089 |
| 4 | langgraph | blackboard | qwen3-30b | Advanced | 0.6222 | 0.9339 | 0.7780 | 0.6845 | 0.8715 | 0.0119 |
| 5 | crewai | orchestrator | qwen3-30b | Advanced | 0.7522 | 0.8974 | 0.8248 | 0.7812 | 0.8684 | 0.0391 |
| 6 | crewai | blackboard | qwen3-30b | Advanced | 0.7535 | 0.8875 | 0.8205 | 0.7803 | 0.8607 | 0.0455 |
| 7 | langgraph | blackboard | Nvidia-Nemotron3-Nano | Basic | 0.4554 | 0.9620 | 0.7087 | 0.5567 | 0.8606 | 0.0138 |
| 8 | langgraph | single_chat | qwen3-30b | Enhanced | 0.5590 | 0.9333 | 0.7461 | 0.6338 | 0.8584 | 0.0262 |
| 9 | langgraph | orchestrator | qwen3-30b | Enhanced | 0.5523 | 0.9347 | 0.7435 | 0.6287 | 0.8582 | 0.0116 |
| 10 | langgraph | single_chat | Nvidia-Nemotron3-Nano | Basic | 0.4789 | 0.9521 | 0.7155 | 0.5735 | 0.8575 | 0.0126 |
