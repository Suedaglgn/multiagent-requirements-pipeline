# Top 10 - HR & Payroll Mobile App - CS quality-focused

Top 10 orchestration combinations for the **HR & Payroll Mobile App** use case, ranked by **CS quality-focused** (0.8 SS / 0.2 CR), averaged over the 5 runs.

The `SS`, `CR`, and `CS ...` columns are means over the 5 runs; the `CS quality-focused (min/median/max/std)` columns describe the **CS quality-focused** score across the 5 runs.

| Rank | Orchestration method | Workflow | LLM | Prompt level | SS | CR | CS balanced | CS quality-focused | CS cost-focused | CS quality-focused (min) | CS quality-focused (median) | CS quality-focused (max) | CS quality-focused (std) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | crewai | blackboard | Nvidia-Nemotron3-Nano | Advanced | 0.8097 | 0.8616 | 0.8356 | 0.8200 | 0.8512 | 0.7707 | 0.7807 | 0.8931 | 0.0592 |
| 2 | crewai | orchestrator | Nvidia-Nemotron3-Nano | Advanced | 0.8104 | 0.8584 | 0.8344 | 0.8200 | 0.8488 | 0.7874 | 0.8129 | 0.8634 | 0.0309 |
| 3 | crewai | sequential | qwen3-30b | Advanced | 0.7980 | 0.8674 | 0.8327 | 0.8119 | 0.8535 | 0.7351 | 0.8360 | 0.8494 | 0.0463 |
| 4 | crewai | orchestrator | qwen3-30b | Advanced | 0.7522 | 0.8974 | 0.8248 | 0.7812 | 0.8684 | 0.7298 | 0.7493 | 0.8695 | 0.0582 |
| 5 | crewai | blackboard | qwen3-30b | Advanced | 0.7535 | 0.8875 | 0.8205 | 0.7803 | 0.8607 | 0.7627 | 0.7709 | 0.8135 | 0.0208 |
| 6 | langgraph | orchestrator | qwen3-30b | Advanced | 0.7261 | 0.9183 | 0.8222 | 0.7646 | 0.8799 | 0.6709 | 0.7857 | 0.8061 | 0.0538 |
| 7 | langgraph | sequential | qwen2.5-72b | Advanced | 0.7656 | 0.6543 | 0.7100 | 0.7433 | 0.6766 | 0.6990 | 0.7536 | 0.7742 | 0.0285 |
| 8 | langgraph | orchestrator | qwen2.5-72b | Advanced | 0.7629 | 0.6596 | 0.7113 | 0.7422 | 0.6803 | 0.6992 | 0.7335 | 0.8184 | 0.0498 |
| 9 | crewai | single_chat | qwen3-30b | Advanced | 0.7234 | 0.8166 | 0.7700 | 0.7420 | 0.7980 | 0.7073 | 0.7536 | 0.7752 | 0.0322 |
| 10 | crewai | blackboard | qwen2.5-72b | Advanced | 0.7766 | 0.5803 | 0.6785 | 0.7374 | 0.6196 | 0.6456 | 0.7488 | 0.8273 | 0.0660 |
