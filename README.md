# Requirement Analysis & Design Estimation: Artifacts

This repository accompanies our research on automating early software engineering with
multi-agent LLM pipelines. It turns a raw project description into two engineering documents:

1. **Requirement Extraction**: a structured requirement list from raw input.
2. **Requirements Analysis (RA)**: a Markdown Software Requirements Specification (`RA.md`).
3. **Design & Estimation (DE)**: a Markdown design and effort-estimation document (`DE.md`).

## Goal of this repository

To make the study's **artifacts** reproducible and reviewable. It contains the exact prompts,
agent roles, output templates, and example inputs and reference outputs used in the experiments.
It does **not** contain the experiment source code or infrastructure, only the artifacts needed
to understand, compare, and reuse the prompt engineering.

The experiments span:

- **Three prompt-engineering levels:** Basic, Enhanced, Advanced.
- **Two agent frameworks:** CrewAI and LangGraph (the same prompt content, packaged per framework).
- **Three pipeline stages:** Requirement Extractor, RA Agent, DE Agent.

## Repository layout

| Folder | Contents | Details |
|--------|----------|---------|
| `prompts/` | The prompts for all three levels x both frameworks x three stages | [prompts/README.md](prompts/README.md) |
| `roles/` | Agent role/persona definitions | [roles/README.md](roles/README.md) |
| `templates/` | Expected output structure for RA, DE, and extractor categories | [templates/README.md](templates/README.md) |
| `examples/` | Experiment inputs and reference (ground-truth) outputs | [examples/README.md](examples/README.md) |

## Where to start

- To see how the prompts differ by level and framework, read [prompts/README.md](prompts/README.md).
- To see the agents that run those prompts, read [roles/README.md](roles/README.md).
- To see the shape of the RA/DE outputs, read [templates/README.md](templates/README.md).
- For concrete inputs and reference outputs, read [examples/README.md](examples/README.md).
