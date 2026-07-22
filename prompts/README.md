# Prompts

The three prompt-engineering levels used in the paper, for both frameworks and all
three agent stages (Requirement Extractor, RA Agent, DE Agent).

```
prompts/
├── basic/
│   ├── crewai/      tasks.yaml            (CrewAI task definitions)
│   └── langgraph/   extractor.txt, ra_agent.txt, de_agent.txt   (same content, system-prompt form)
├── enhanced/        (same layout)
└── advanced/        (same layout)
```

## Same prompts, framework-specific packaging

At every level the two frameworks use the **same prompt content**: identical section lists,
guidelines, and expected outputs. They differ only in how each framework expects the prompt
to be packaged and configured:

- **CrewAI** (`crewai/tasks.yaml`): each stage is a task with a `description` and an
  `expected_output`. It keeps the runtime placeholders (`{project_description}`,
  `{approved_requirements}`, `{approved_ra}`, `{feedback}`) and the feedback-injection line,
  which are filled in at execution time. The agent persona is defined separately in `roles/`.
- **LangGraph** (`langgraph/*.txt`): the same task body delivered as a single system prompt.
  It prepends the agent persona line, and drops the CrewAI-only runtime placeholders and
  feedback-injection line (LangGraph supplies those through its own state). The text is
  otherwise identical, just flattened into one plain prompt.

Because the instruction text is the same, choosing a framework changes only the packaging,
not the prompt engineering being evaluated.

## Prompt levels (summary)

| Level | Requirement Extractor | RA Agent | DE Agent |
|-------|-----------------------|----------|----------|
| Basic | free-form list, categories not enumerated | SRS with guidelines only (no section list) | free-form document (no section list) |
| Enhanced | 5 fixed categories + extraction guidelines | 8 section titles | 14 section titles |
| Advanced | 5 fixed categories + guidelines (structured lists, no prose) | 8 sections with detailed per-section guidance | 14 sections with detailed per-section guidance |
