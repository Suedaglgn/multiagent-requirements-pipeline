<!--
Template: DE (Design & Estimation) output structure
Note: The structure is embedded in the DE Advanced prompt and surfaced here so the expected
DE.md shape is easy to locate. Basic-level runs do not enforce this section list (guidelines
only); Enhanced uses section titles only.
-->

# DE Output Template (Design & Estimation)

Output format: a single Markdown document (DE.md content), no preamble. Use Markdown tables
for all structured data and reference RA requirement IDs throughout.

Sections (Advanced level):

1. Executive Summary
2. System Architecture (pattern justification, ASCII diagram, component list)
3. Technology Stack (Frontend, Backend, Database tables with versions and justifications)
4. Infrastructure & Deployment (cloud services, CI/CD, environments)
5. Data Flows for key use cases (step-by-step)
6. Database Schema Overview
7. API Surface Overview
8. Development Effort Estimation (module T-shirt sizing, phased delivery, team composition)
9. Technical Risks & Mitigation
10. Security Architecture
11. Monitoring & Observability
12. Cost Estimation (monthly breakdown in USD)
13. Recommendations & Next Steps
14. Traceability Matrix

Guidelines:
- Write a **long, detailed Design & Estimation document** in Markdown based on the RA.
- Output only the Markdown document (DE.md content), no preamble.

Expected output: "A long and detailed, comprehensive Markdown DE document with architecture,
tech stack, estimates, costs, and traceability. Output only the Markdown (DE.md content), no
preamble."
