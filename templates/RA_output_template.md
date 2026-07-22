<!--
Template: RA (Requirements Analysis / SRS) output structure
Note: The structure is embedded in the RA Advanced prompt and surfaced here so the expected
RA.md shape is easy to locate. Basic-level runs do not enforce this section list (guidelines
only); Enhanced uses section titles only.
-->

# RA Output Template (Software Requirements Specification, SRS)

Output format: a single Markdown document (RA.md content), no preamble. Use Markdown tables
with hierarchical IDs like `FR-AUTH-001` for all requirements.

Sections (Advanced level):

1. Introduction (purpose, scope, definitions table, audience)
2. Functional Requirements: organized by feature domain, each in a table (ID, Title, Description, Priority). Break features into many granular requirements and infer implied ones.
3. Non-Functional Requirements: Security, Performance, Availability, Scalability, Usability, Maintainability. Each in a table with measurable metrics.
4. Technical Constraints & Integration
5. User Personas & Use Cases (structured tables with main flows)
6. Edge Cases, Risks, and Gaps
7. Assumptions
8. Traceability Matrix

Guidelines:
- Ensure every requirement is SMART (Specific, Measurable, Achievable, Relevant, Testable).
- Expand bullet points into professional, development-team-ready prose.
- Use English only.
- Output only the Markdown document (RA.md content), no preamble.

Expected output: "A long and detailed, comprehensive Markdown SRS document with many detailed
requirements in tables. Output only the Markdown (RA.md content), no preamble."
