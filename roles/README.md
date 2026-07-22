# Agent Roles

Role/persona definitions for every agent in the pipeline, as used verbatim in the
experiments (CrewAI role definitions and LangGraph system prompts).

| Role | File | Frameworks | Paper term |
|------|------|------------|------------|
| Requirement Extractor | `requirement_extractor.md` | CrewAI + LangGraph | Requirement Extractor |
| Requirements Analysis Specialist | `ra_agent.md` | CrewAI + LangGraph | RA Agent |
| Design & Estimation Specialist | `de_agent.md` | CrewAI + LangGraph | DE Agent |
| Central Orchestrator | `orchestrator.md` | CrewAI + LangGraph | (Orchestrator workflow coordinator) |
| Chat Agent | `chat_agent.md` | CrewAI only | (Single-Chat workflow persona) |

**Chat Agent is CrewAI-only.** In CrewAI's Single-Chat workflow one dedicated persona runs all
three stages, so it needs its own role definition. LangGraph's Single-Chat workflow has no
separate chat persona; it reuses the Extractor / RA / DE prompts together in a single run, so
there is no equivalent LangGraph system prompt to publish.

The three paper prompt levels (Basic / Enhanced / Advanced) that these roles execute are
in `prompts/`. The RA/DE output structures are in `templates/`.
