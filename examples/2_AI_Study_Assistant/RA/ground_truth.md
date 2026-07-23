# Software Requirements Specification — Nerd AI

## 1. Introduction

### 1.1 Purpose
This document defines the functional and non-functional requirements for **Nerd AI**, an AI-powered educational assistant application that helps students with writing, problem-solving, language learning, summarization, coding, and trivia. It serves as the authoritative reference for design, development, testing, and acceptance.

### 1.2 Scope
Nerd AI is a cross-platform mobile application (iOS & Android) with a cloud backend. It leverages large language models and computer-vision APIs to deliver six core feature domains: Scan & Solve, Writing Generation, Language Skills, Rewriting, Coding Assistance, and Trivia Quest. The system shall support freemium and subscription monetization models.

### 1.3 Definitions

| Term | Definition |
|------|-----------|
| LLM | Large Language Model used for text generation and reasoning |
| OCR | Optical Character Recognition for extracting text from images |
| SRS | Software Requirements Specification |
| API | Application Programming Interface |
| RBAC | Role-Based Access Control |
| WCAG | Web Content Accessibility Guidelines |

### 1.4 Intended Audience
Development engineers, QA engineers, UI/UX designers, product managers, and external stakeholders involved in funding or compliance review.

---

## 2. Functional Requirements

### 2.1 Authentication & User Management

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-AUTH-001 | User Registration | The system shall allow users to register via email/password, Google, or Apple SSO. | High |
| FR-AUTH-002 | Email Verification | The system shall send a verification email within 30 seconds of registration and block feature access until verified. | High |
| FR-AUTH-003 | Login | The system shall authenticate users via stored credentials or SSO tokens and issue a session JWT valid for 7 days. | High |
| FR-AUTH-004 | Password Reset | The system shall allow users to request a password-reset link that expires after 15 minutes. | High |
| FR-AUTH-005 | Profile Management | Users shall be able to update display name, avatar, preferred language, and notification preferences. | Medium |
| FR-AUTH-006 | Account Deletion | The system shall permanently delete all user data within 30 days of a deletion request, per GDPR. | High |
| FR-AUTH-007 | Session Management | The system shall allow users to view and revoke active sessions on other devices. | Medium |

### 2.2 Scan & Solve

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-SCAN-001 | Camera Capture | The app shall open the device camera and allow the user to photograph a question (text, math, diagram). | High |
| FR-SCAN-002 | Gallery Upload | The system shall allow users to upload an image from the device gallery as an alternative to camera capture. | High |
| FR-SCAN-003 | OCR Extraction | The system shall extract text from the captured image using OCR with ≥ 95 % character-level accuracy on printed text. | High |
| FR-SCAN-004 | Math Expression Recognition | The system shall recognize handwritten and printed mathematical expressions (LaTeX-level) from scanned images. | High |
| FR-SCAN-005 | Step-by-Step Solution | The system shall return a step-by-step explanation for the recognized question within 5 seconds of submission. | High |
| FR-SCAN-006 | Solution Formatting | Solutions shall render LaTeX for math, syntax-highlighted code for programming, and structured prose for text questions. | High |
| FR-SCAN-007 | Alternative Methods | When applicable, the system shall offer at least one alternative solution method for math and science problems. | Medium |
| FR-SCAN-008 | Scan History | The system shall persist the last 100 scanned questions per user, searchable by date and keyword. | Medium |
| FR-SCAN-009 | Image Crop & Rotate | The user shall be able to crop and rotate an image before submission to improve OCR accuracy. | Medium |
| FR-SCAN-010 | Confidence Indicator | The system shall display a confidence score when OCR accuracy falls below 90 %, prompting the user to re-scan. | Low |

### 2.3 Writing Generation

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-WRITE-001 | Essay Generation | The system shall generate an essay based on a user-supplied topic, thesis, and optional outline. | High |
| FR-WRITE-002 | Blog Post Generation | The system shall generate blog posts with title suggestions, headings, and body paragraphs. | High |
| FR-WRITE-003 | Presentation Script | The system shall generate slide-by-slide presentation scripts including speaker notes. | High |
| FR-WRITE-004 | Word Count Control | The user shall specify a target word count (100–5 000); output length shall be within ±10 % of the target. | High |
| FR-WRITE-005 | Tone Selection | The user shall choose a writing tone from at least: Academic, Casual, Professional, Persuasive, Creative. | High |
| FR-WRITE-006 | Audience Targeting | The user shall specify a target audience (e.g., high-school, undergraduate, general public) to adjust vocabulary complexity. | Medium |
| FR-WRITE-007 | Outline Preview | Before full generation, the system shall present an editable outline for user approval. | Medium |
| FR-WRITE-008 | Citation Suggestions | The system shall suggest placeholder citations in APA/MLA format where factual claims are made. | Medium |
| FR-WRITE-009 | Regeneration | The user shall be able to regenerate any section without losing edits to other sections. | High |
| FR-WRITE-010 | Export Formats | Generated content shall be exportable as PDF, DOCX, and plain-text Markdown. | Medium |

### 2.4 Language Skills

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-LANG-001 | Grammar Check | The system shall highlight grammatical errors in user-supplied text and offer corrections with explanations. | High |
| FR-LANG-002 | Spelling Check | The system shall detect and suggest corrections for spelling errors in at least 10 supported languages. | High |
| FR-LANG-003 | Text Translation | The system shall translate text between at least 30 language pairs with BLEU score ≥ 40 on standard benchmarks. | High |
| FR-LANG-004 | Contextual Hints | For each grammar correction, the system shall provide a brief rule explanation (e.g., subject-verb agreement). | High |
| FR-LANG-005 | Vocabulary Builder | The system shall offer word-of-the-day and vocabulary quizzes based on the user's proficiency level. | Medium |
| FR-LANG-006 | Readability Score | The system shall display a readability score (Flesch-Kincaid) after grammar analysis. | Low |
| FR-LANG-007 | Language Detection | The system shall auto-detect the input language before performing grammar or translation operations. | High |

### 2.5 Rewriting

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-REW-001 | Paraphrase | The system shall rewrite user text in alternative wording while preserving original meaning. | High |
| FR-REW-002 | Simplify | The system shall reduce text complexity to a user-selected reading level (e.g., Grade 5, Grade 8). | High |
| FR-REW-003 | Continue Text | Given an incomplete passage, the system shall generate a coherent continuation matching tone and style. | High |
| FR-REW-004 | Draft Fine-Tuning | The user shall supply a draft and improvement instructions; the system shall return an improved version. | High |
| FR-REW-005 | Diff View | The system shall present original vs. rewritten text in a side-by-side diff view highlighting changes. | Medium |
| FR-REW-006 | Undo / Redo | The user shall be able to undo and redo rewriting operations up to 10 steps. | Medium |
| FR-REW-007 | Tone Shift | The user shall be able to rewrite content to shift its tone (e.g., formal → casual). | Medium |

### 2.6 Coding Assistance

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-CODE-001 | Code Explanation | The system shall accept a code snippet and return a line-by-line or block-level explanation in plain English. | High |
| FR-CODE-002 | Language Support | The system shall support at least 15 programming languages including Python, Java, C++, JavaScript, and SQL. | High |
| FR-CODE-003 | Syntax Highlighting | All code displayed in the app shall be syntax-highlighted according to the detected language. | High |
| FR-CODE-004 | Code Optimization | The system shall suggest optimized versions of user code with explanations of improvements. | High |
| FR-CODE-005 | Bug Detection | The system shall identify potential bugs, logical errors, and anti-patterns in submitted code. | High |
| FR-CODE-006 | Code Generation | Given a natural-language prompt, the system shall generate functional code in the user's selected language. | Medium |
| FR-CODE-007 | Run Sandbox | The system shall provide a sandboxed environment to execute code snippets (Python, JS) and display output. | Medium |
| FR-CODE-008 | Learning Paths | The system shall offer structured beginner-to-advanced coding tutorials for at least 5 languages. | Low |
| FR-CODE-009 | Copy to Clipboard | All code output blocks shall include a one-tap copy-to-clipboard button. | High |

### 2.7 Trivia Quest

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-TRIV-001 | Topic Selection | The user shall choose trivia topics from at least 10 categories (Science, History, Math, Literature, etc.). | High |
| FR-TRIV-002 | Question Generation | The system shall dynamically generate trivia questions using the LLM, avoiding repeated questions within a session. | High |
| FR-TRIV-003 | Difficulty Levels | The user shall select Easy, Medium, or Hard difficulty; questions shall be calibrated accordingly. | High |
| FR-TRIV-004 | Scoring & Streaks | The system shall track scores, streaks, and personal bests per user. | Medium |
| FR-TRIV-005 | Explanation on Answer | After each answer, the system shall display the correct answer and a brief educational explanation. | High |
| FR-TRIV-006 | Leaderboard | The system shall maintain a global and friends-based leaderboard updated in near-real-time. | Low |
| FR-TRIV-007 | Daily Challenge | The system shall publish one daily challenge quiz available to all users with a 24-hour window. | Medium |

---

## 3. Non-Functional Requirements

### 3.1 Security

| ID | Requirement | Metric |
|----|------------|--------|
| NFR-SEC-001 | All data in transit shall be encrypted using TLS 1.2+. | 100 % HTTPS enforcement |
| NFR-SEC-002 | Passwords shall be hashed with bcrypt (cost factor ≥ 12). | Verified via security audit |
| NFR-SEC-003 | The system shall enforce rate limiting (100 req/min per user) to prevent abuse. | < 0.1 % false-positive blocks |
| NFR-SEC-004 | User PII shall be encrypted at rest using AES-256. | Audit-confirmed |
| NFR-SEC-005 | The system shall pass OWASP Top-10 vulnerability assessment before each release. | Zero critical findings |

### 3.2 Performance

| ID | Requirement | Metric |
|----|------------|--------|
| NFR-PERF-001 | Scan & Solve end-to-end latency (image upload → solution displayed). | ≤ 5 seconds (p95) |
| NFR-PERF-002 | Text generation requests (writing, rewriting, code) response time. | ≤ 8 seconds (p95) |
| NFR-PERF-003 | App cold-start time on mid-range devices. | ≤ 3 seconds |
| NFR-PERF-004 | API throughput under normal load. | ≥ 500 concurrent users |

### 3.3 Availability & Scalability

| ID | Requirement | Metric |
|----|------------|--------|
| NFR-AVL-001 | System uptime. | ≥ 99.9 % monthly |
| NFR-AVL-002 | Automatic horizontal scaling of backend services. | Scale-out within 60 s of threshold breach |
| NFR-AVL-003 | Disaster recovery RTO / RPO. | RTO ≤ 1 hour, RPO ≤ 15 minutes |

### 3.4 Usability

| ID | Requirement | Metric |
|----|------------|--------|
| NFR-USB-001 | The app shall achieve a System Usability Scale (SUS) score ≥ 80 in user testing. | SUS ≥ 80 |
| NFR-USB-002 | All primary features shall be reachable within 2 taps from the home screen. | ≤ 2 taps |
| NFR-USB-003 | The app shall conform to WCAG 2.1 AA accessibility standards. | AA compliance verified |

### 3.5 Maintainability

| ID | Requirement | Metric |
|----|------------|--------|
| NFR-MNT-001 | Codebase test coverage. | ≥ 80 % line coverage |
| NFR-MNT-002 | All APIs shall be documented via OpenAPI 3.0 spec. | 100 % endpoint coverage |
| NFR-MNT-003 | Deployment pipeline shall support zero-downtime rolling updates. | Zero-downtime verified per release |

---

## 4. Technical Constraints & Integration

| ID | Constraint |
|----|-----------|
| TC-001 | The mobile app shall be built using a cross-platform framework (Flutter or React Native) targeting iOS 15+ and Android 10+. |
| TC-002 | The backend shall expose RESTful APIs; real-time features (leaderboard, streaks) shall use WebSockets. |
| TC-003 | LLM inference shall be served via a managed AI gateway (e.g., OpenAI API, Azure OpenAI) with fallback routing. |
| TC-004 | OCR shall use a dedicated vision model (e.g., Google Vision API or custom fine-tuned model). |
| TC-005 | User data shall be stored in a managed relational database (PostgreSQL) with read replicas. |
| TC-006 | Media assets (images) shall be stored in object storage (S3-compatible) with CDN distribution. |
| TC-007 | The code sandbox (FR-CODE-007) shall run in ephemeral containers with 5-second execution timeout and no network access. |
| TC-008 | The system shall integrate with a payment provider (Stripe / Google Play Billing / Apple IAP) for subscriptions. |

---

## 5. User Personas & Use Cases

### 5.1 Personas

| Persona | Age | Description |
|---------|-----|-------------|
| **High-School Student (Sam)** | 15–18 | Needs homework help, essay writing, and math problem-solving. Limited budget; uses free tier. |
| **University Student (Priya)** | 19–24 | Requires academic writing, citation help, coding tutorials, and language translation for multilingual coursework. |
| **Self-Learner (Carlos)** | 25–40 | Uses the app to learn programming and improve English writing skills outside a formal education setting. |
| **Educator (Dr. Lee)** | 35–55 | Uses trivia and writing features to create classroom materials and quizzes. |

### 5.2 Use Cases

| UC-ID | Actor | Goal | Main Flow |
|-------|-------|------|-----------|
| UC-001 | Sam | Solve a math problem by scanning | 1. Open app → 2. Tap "Scan & Solve" → 3. Capture photo → 4. Crop if needed → 5. Submit → 6. View step-by-step solution → 7. Save to history. |
| UC-002 | Priya | Generate an academic essay | 1. Open "Write" → 2. Enter topic & thesis → 3. Select Academic tone, 1 500 words → 4. Review outline → 5. Approve → 6. Review essay → 7. Export as DOCX. |
| UC-003 | Carlos | Learn Python basics | 1. Open "Code" → 2. Select Python learning path → 3. Read tutorial → 4. Write code in sandbox → 5. Run → 6. View output & explanation. |
| UC-004 | Priya | Translate and grammar-check text | 1. Open "Language" → 2. Paste text → 3. Auto-detect language → 4. Translate to English → 5. Run grammar check → 6. Review corrections with explanations. |
| UC-005 | Sam | Play a trivia quest | 1. Open "Trivia" → 2. Select Science, Medium difficulty → 3. Answer questions → 4. View score & explanations → 5. Check leaderboard. |
| UC-006 | Dr. Lee | Rewrite content for simpler audience | 1. Open "Rewrite" → 2. Paste paragraph → 3. Select "Simplify" to Grade 5 → 4. Review diff view → 5. Copy result. |

---

## 6. Edge Cases, Risks & Gaps

| ID | Category | Description | Mitigation |
|----|----------|-------------|------------|
| RISK-001 | Accuracy | LLM may generate factually incorrect solutions ("hallucinations"). | Implement confidence scoring, disclaimers, and user-feedback loop for flagging errors. |
| RISK-002 | OCR Failure | Handwritten text or low-quality images may produce unusable OCR output. | Provide image-quality checks, confidence indicator (FR-SCAN-010), and manual text-input fallback. |
| RISK-003 | Abuse | Users may use writing features for plagiarism or academic dishonesty. | Add academic-integrity disclaimers; optionally integrate plagiarism-detection API. |
| RISK-004 | Latency Spikes | High LLM inference demand during exam periods may cause timeouts. | Implement request queuing, auto-scaling (NFR-AVL-002), and graceful degradation messaging. |
| RISK-005 | Content Safety | LLM may produce harmful, biased, or inappropriate content. | Apply output content filters and moderation layer before displaying results to users. |
| RISK-006 | Sandbox Escape | Malicious code in the sandbox could attempt to access host resources. | Enforce container isolation, read-only filesystem, no network, and strict resource limits (TC-007). |
| GAP-001 | Offline Mode | Requirements do not currently address offline functionality. | Clarify with stakeholders whether offline caching of past results is required. |
| GAP-002 | Accessibility | Detailed screen-reader flow and voice-input support are not yet specified. | Conduct accessibility audit and expand requirements before beta release. |

---

## 7. Assumptions

| ID | Assumption |
|----|-----------|
| ASMP-001 | Users have a stable internet connection; the app is primarily online. |
| ASMP-002 | The AI/LLM provider guarantees ≥ 99.9 % API availability under the contracted SLA. |
| ASMP-003 | Users grant camera and storage permissions required for Scan & Solve functionality. |
| ASMP-004 | Subscription pricing and tier definitions will be provided by the business team before development of the paywall module. |
| ASMP-005 | Content moderation policies and regional compliance requirements (COPPA, GDPR, KVKK) will be finalized during the design phase. |
| ASMP-006 | Third-party OCR and LLM APIs will remain backward-compatible during the initial release cycle. |

---

## 8. Traceability Matrix

| Requirement ID | Use Case | Persona | Test Strategy |
|---------------|----------|---------|---------------|
| FR-SCAN-001 – 010 | UC-001 | Sam, Priya | Integration test: image → OCR → solution pipeline; accuracy benchmarks on 500-image dataset. |
| FR-WRITE-001 – 010 | UC-002 | Priya, Dr. Lee | End-to-end test: topic input → outline → generated essay; word-count & tone validation. |
| FR-LANG-001 – 007 | UC-004 | Priya, Carlos | Unit tests per grammar rule; BLEU score regression tests for translation. |
| FR-REW-001 – 007 | UC-006 | Dr. Lee, Carlos | A/B comparison: original vs. rewritten; readability-score delta verification. |
| FR-CODE-001 – 009 | UC-003 | Carlos, Priya | Functional test: code input → explanation accuracy; sandbox execution & timeout tests. |
| FR-TRIV-001 – 007 | UC-005 | Sam | Session test: no duplicate questions; difficulty calibration via answer-rate analysis. |
| FR-AUTH-001 – 007 | All | All | Security test: registration, login, SSO, password reset flows; penetration test on auth endpoints. |
| NFR-SEC-001 – 005 | All | All | Automated OWASP scan; TLS certificate validation; bcrypt hash verification. |
| NFR-PERF-001 – 004 | All | All | Load test with k6/Locust simulating 500+ concurrent users; p95 latency assertions. |
| NFR-AVL-001 – 003 | All | All | Chaos engineering (pod kill, zone failover); uptime monitoring via Datadog/PagerDuty. |
| NFR-USB-001 – 003 | All | All | Usability lab sessions (n ≥ 15); automated accessibility audit (axe-core). |
| NFR-MNT-001 – 003 | — | Dev Team | CI pipeline coverage gate; OpenAPI spec linting; canary deployment verification. |
