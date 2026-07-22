# Design & Estimation Document — Nerd AI

## 1. Executive Summary

This document translates the approved Requirements Analysis (RA_2) for **Nerd AI** into a concrete system design, technology selection, effort estimation, and cost projection. Nerd AI is a cross-platform mobile application powered by LLMs and computer-vision APIs, delivering six core feature domains — Scan & Solve, Writing Generation, Language Skills, Rewriting, Coding Assistance, and Trivia Quest — under a freemium/subscription model. The design targets ≤ 5 s scan-to-solution latency (NFR-PERF-001), 99.9 % uptime (NFR-AVL-001), and OWASP Top-10 compliance (NFR-SEC-005). Estimated delivery spans **5 phases over ~28 weeks** with a peak team of 12.

---

## 2. System Architecture

### 2.1 Pattern Justification

A **microservices architecture** behind an API gateway is chosen to satisfy independent scaling of LLM-heavy modules (FR-SCAN-005, FR-WRITE-001, FR-CODE-001) and isolation of the code sandbox (TC-007). Event-driven messaging (NATS/Kafka) decouples scoring and leaderboard updates (FR-TRIV-004, FR-TRIV-006) from synchronous request paths.

### 2.2 High-Level Diagram

```
┌──────────────┐       HTTPS/WSS        ┌──────────────────┐
│  Mobile App  │◄──────────────────────►│   API Gateway /  │
│ (Flutter)    │                         │   Load Balancer   │
└──────────────┘                         └────────┬─────────┘
                                                  │
                 ┌────────────┬───────────┬───────┴────┬──────────────┐
                 ▼            ▼           ▼            ▼              ▼
          ┌───────────┐ ┌──────────┐ ┌─────────┐ ┌──────────┐ ┌───────────┐
          │ Auth Svc  │ │ AI Svc   │ │ Code Svc│ │ Trivia   │ │ User /    │
          │ (JWT/SSO) │ │ (LLM +  │ │ (Sandbox│ │ Svc      │ │ Profile   │
          │           │ │  OCR)    │ │  Runner)│ │          │ │ Svc       │
          └─────┬─────┘ └────┬─────┘ └────┬────┘ └────┬─────┘ └─────┬─────┘
                │             │            │           │              │
                ▼             ▼            ▼           ▼              ▼
          ┌─────────────────────────────────────────────────────────────┐
          │                     PostgreSQL (RDS)                        │
          │              + Redis Cache + S3 Object Store                │
          └─────────────────────────────────────────────────────────────┘
```

### 2.3 Component List

| Component | Responsibility | RA References |
|-----------|---------------|---------------|
| API Gateway | Routing, rate limiting (NFR-SEC-003), TLS termination | NFR-SEC-001, NFR-SEC-003 |
| Auth Service | Registration, SSO, JWT issuance, session mgmt | FR-AUTH-001 – 007 |
| AI Service | LLM orchestration, OCR, writing, rewriting, language | FR-SCAN-001 – 010, FR-WRITE-001 – 010, FR-LANG-001 – 007, FR-REW-001 – 007 |
| Code Service | Code explanation, generation, sandbox execution | FR-CODE-001 – 009, TC-007 |
| Trivia Service | Question generation, scoring, leaderboard via WebSocket | FR-TRIV-001 – 007, TC-002 |
| User/Profile Service | Profile CRUD, preferences, account deletion | FR-AUTH-005, FR-AUTH-006 |
| Media Store (S3 + CDN) | Image uploads, scan history assets | TC-006, FR-SCAN-008 |
| Message Broker (NATS) | Async events — scoring, notifications, analytics | NFR-AVL-002 |

---

## 3. Technology Stack

### 3.1 Frontend

| Technology | Version | Justification |
|-----------|---------|---------------|
| Flutter | 3.22 | Cross-platform iOS 15+ / Android 10+ (TC-001); single codebase; rich widget library |
| Dart | 3.4 | Flutter's native language |
| flutter_latex | 4.x | LaTeX rendering for FR-SCAN-006 |
| flutter_highlight | 0.7 | Syntax highlighting for FR-CODE-003 |

### 3.2 Backend

| Technology | Version | Justification |
|-----------|---------|---------------|
| Node.js (NestJS) | 20 LTS / 10.x | TypeScript-first microservices, decorator-based DI, OpenAPI auto-gen (NFR-MNT-002) |
| NATS JetStream | 2.10 | Lightweight event streaming for leaderboard & scoring |
| gVisor / Firecracker | latest | Sandbox isolation for code execution (TC-007, RISK-006) |
| OpenAI API / Azure OpenAI | GPT-4o | LLM inference with fallback routing (TC-003) |
| Google Cloud Vision | v1 | OCR extraction (TC-004, FR-SCAN-003) |

### 3.3 Database & Storage

| Technology | Version | Justification |
|-----------|---------|---------------|
| PostgreSQL (RDS) | 16 | Relational store, read replicas (TC-005) |
| Redis | 7.2 | Session cache, rate-limit counters, leaderboard sorted sets |
| Amazon S3 + CloudFront | — | Image/media storage with CDN (TC-006) |

---

## 4. Infrastructure & Deployment

| Aspect | Choice | RA Reference |
|--------|--------|-------------|
| Cloud Provider | AWS (primary) | NFR-AVL-001 |
| Container Orchestration | EKS (Kubernetes) | NFR-AVL-002 — auto-scale within 60 s |
| CI/CD | GitHub Actions → ECR → ArgoCD | NFR-MNT-003 — zero-downtime rolling updates |
| Environments | dev, staging, prod (multi-AZ) | NFR-AVL-003 — RTO ≤ 1 h, RPO ≤ 15 min |
| IaC | Terraform | Reproducible infra provisioning |
| CDN | CloudFront | TC-006 |
| Payments | Stripe + Apple IAP + Google Play Billing | TC-008 |
| Secrets Management | AWS Secrets Manager | NFR-SEC-004 |

---

## 5. Data Flows for Key Use Cases

### 5.1 UC-001 — Scan & Solve (Sam)

1. User captures/uploads image (FR-SCAN-001/002) → Flutter sends multipart POST to API Gateway.
2. Gateway authenticates JWT (FR-AUTH-003), forwards to AI Service.
3. AI Service stores image in S3 (TC-006), calls Google Vision OCR (FR-SCAN-003/004).
4. OCR result checked for confidence; if < 90 %, return confidence indicator (FR-SCAN-010).
5. OCR text sent to LLM (TC-003) with step-by-step prompt (FR-SCAN-005).
6. LLM response formatted (LaTeX/code highlighting — FR-SCAN-006) and returned.
7. Result persisted in scan_history table (FR-SCAN-008). Total target: ≤ 5 s (NFR-PERF-001).

### 5.2 UC-003 — Code Sandbox (Carlos)

1. User writes code in-app editor, taps Run (FR-CODE-007).
2. Code Service spins ephemeral gVisor container — no network, read-only FS, 5 s timeout (TC-007).
3. stdout/stderr captured, returned with syntax highlighting (FR-CODE-003).
4. Container destroyed immediately after execution.

---

## 6. Database Schema Overview

| Table | Key Columns | RA Reference |
|-------|------------|--------------|
| `users` | id, email, password_hash, provider, display_name, avatar_url, lang_pref, created_at, deleted_at | FR-AUTH-001 – 006 |
| `sessions` | id, user_id, device_info, jwt_jti, expires_at, revoked | FR-AUTH-003, FR-AUTH-007 |
| `scan_history` | id, user_id, image_url, ocr_text, solution, confidence, created_at | FR-SCAN-003 – 008 |
| `writing_projects` | id, user_id, type, topic, tone, audience, word_target, outline_json, content, status | FR-WRITE-001 – 009 |
| `rewrite_history` | id, user_id, original_text, rewritten_text, mode, created_at | FR-REW-001 – 007 |
| `code_sessions` | id, user_id, language, input_code, output, explanation, created_at | FR-CODE-001 – 007 |
| `trivia_sessions` | id, user_id, topic, difficulty, score, streak, completed_at | FR-TRIV-001 – 005 |
| `trivia_leaderboard` | user_id, period, score (Redis sorted set, persisted daily) | FR-TRIV-006 |
| `subscriptions` | id, user_id, plan, provider, status, current_period_end | TC-008 |

All PII columns encrypted at rest (AES-256) per NFR-SEC-004. Passwords hashed with bcrypt cost ≥ 12 (NFR-SEC-002).

---

## 7. API Surface Overview

| Method | Endpoint | Service | RA Reference |
|--------|---------|---------|-------------|
| POST | `/auth/register` | Auth | FR-AUTH-001 |
| POST | `/auth/login` | Auth | FR-AUTH-003 |
| POST | `/auth/password-reset` | Auth | FR-AUTH-004 |
| DELETE | `/auth/account` | Auth | FR-AUTH-006 |
| POST | `/scan/solve` | AI | FR-SCAN-001 – 006 |
| GET | `/scan/history` | AI | FR-SCAN-008 |
| POST | `/write/generate` | AI | FR-WRITE-001 – 005 |
| POST | `/write/regenerate-section` | AI | FR-WRITE-009 |
| POST | `/language/check` | AI | FR-LANG-001, 002, 004 |
| POST | `/language/translate` | AI | FR-LANG-003 |
| POST | `/rewrite` | AI | FR-REW-001 – 004 |
| POST | `/code/explain` | Code | FR-CODE-001 |
| POST | `/code/run` | Code | FR-CODE-007 |
| WS | `/trivia/session` | Trivia | FR-TRIV-001 – 005 |
| WS | `/trivia/leaderboard` | Trivia | FR-TRIV-006 |

All endpoints documented via OpenAPI 3.0 (NFR-MNT-002). Rate-limited at gateway: 100 req/min/user (NFR-SEC-003).

---

## 8. Development Effort Estimation

### 8.1 Module T-Shirt Sizing

| Module | Size | Est. Weeks | RA Reference |
|--------|------|-----------|-------------|
| Auth & User Management | M | 3 | FR-AUTH-001 – 007 |
| Scan & Solve (OCR + LLM) | XL | 5 | FR-SCAN-001 – 010 |
| Writing Generation | L | 4 | FR-WRITE-001 – 010 |
| Language Skills | L | 4 | FR-LANG-001 – 007 |
| Rewriting | M | 3 | FR-REW-001 – 007 |
| Coding Assistance + Sandbox | XL | 5 | FR-CODE-001 – 009, TC-007 |
| Trivia Quest | L | 4 | FR-TRIV-001 – 007 |
| Subscription / Payments | M | 3 | TC-008 |
| Infrastructure & CI/CD | M | 3 | NFR-AVL, NFR-MNT |
| Mobile App Shell & Navigation | M | 3 | NFR-USB-001 – 003, TC-001 |

### 8.2 Phased Delivery

| Phase | Duration | Deliverables |
|-------|----------|-------------|
| **P1 — Foundation** | Weeks 1–4 | Auth, App Shell, Infra/CI-CD, DB schema |
| **P2 — Core AI** | Weeks 5–12 | Scan & Solve, Writing Generation, Rewriting |
| **P3 — Extended AI** | Weeks 13–18 | Language Skills, Coding Assistance + Sandbox |
| **P4 — Engagement** | Weeks 19–23 | Trivia Quest, Leaderboard, Subscription/Payments |
| **P5 — Hardening** | Weeks 24–28 | Performance tuning, security audit, accessibility, beta |

### 8.3 Team Composition

| Role | Count |
|------|-------|
| Flutter Developer | 2 |
| Backend Engineer (Node.js) | 3 |
| ML / AI Engineer | 2 |
| DevOps / SRE | 1 |
| QA Engineer | 2 |
| UI/UX Designer | 1 |
| Product Manager | 1 |
| **Total** | **12** |

---

## 9. Technical Risks & Mitigation

| Risk ID | Risk | Likelihood | Impact | Mitigation | RA Ref |
|---------|------|-----------|--------|-----------|--------|
| TR-001 | LLM hallucinations in solutions | High | High | Confidence scoring, user-feedback flags, disclaimer banners | RISK-001 |
| TR-002 | OCR failure on handwritten input | Medium | High | Image-quality pre-check, confidence indicator (FR-SCAN-010), manual fallback | RISK-002 |
| TR-003 | Sandbox escape | Low | Critical | gVisor isolation, no network, read-only FS, 5 s timeout, security audit | RISK-006, TC-007 |
| TR-004 | LLM API latency spikes during peak | Medium | High | Request queuing, auto-scaling, response caching, fallback model routing | RISK-004, NFR-AVL-002 |
| TR-005 | Harmful content generation | Medium | High | Output content filter + moderation layer before display | RISK-005 |
| TR-006 | Vendor lock-in to single LLM provider | Medium | Medium | Abstraction layer supporting OpenAI + Azure + Anthropic | TC-003 |

---

## 10. Security Architecture

| Layer | Mechanism | RA Reference |
|-------|-----------|-------------|
| Transport | TLS 1.2+ enforced on all endpoints | NFR-SEC-001 |
| Authentication | JWT (7-day expiry) + OAuth 2.0 SSO (Google, Apple) | FR-AUTH-003 |
| Password Storage | bcrypt, cost factor ≥ 12 | NFR-SEC-002 |
| Rate Limiting | 100 req/min/user at API Gateway (token bucket) | NFR-SEC-003 |
| Data at Rest | AES-256 column-level encryption for PII | NFR-SEC-004 |
| Vulnerability Mgmt | OWASP Top-10 scan per release; dependency audit (Snyk) | NFR-SEC-005 |
| Sandbox | gVisor, no network, read-only FS, 5 s kill | TC-007 |
| Content Safety | LLM output moderation filter before client delivery | RISK-005 |
| GDPR/KVKK | 30-day hard-delete pipeline; data-export endpoint | FR-AUTH-006, ASMP-005 |

---

## 11. Monitoring & Observability

| Concern | Tool | Detail |
|---------|------|--------|
| Metrics | Prometheus + Grafana | p95 latency dashboards per service; scale-trigger alerts |
| Logging | EFK (Elasticsearch, Fluentd, Kibana) | Structured JSON logs; 30-day retention |
| Tracing | OpenTelemetry + Jaeger | Distributed traces across gateway → service → LLM/OCR |
| Uptime | AWS CloudWatch + PagerDuty | 99.9 % SLA alerting (NFR-AVL-001) |
| Error Tracking | Sentry | Mobile + backend crash reporting |
| SLOs | Latency ≤ 5 s (scan), ≤ 8 s (text gen) | NFR-PERF-001, NFR-PERF-002 |

---

## 12. Cost Estimation (Monthly, USD)

| Item | Specification | Est. Cost |
|------|-------------|-----------|
| EKS Cluster (3 nodes m6i.xlarge) | Compute for backend services | $750 |
| RDS PostgreSQL (db.r6g.xlarge, Multi-AZ) | Primary + read replica | $900 |
| Redis (ElastiCache r6g.large) | Session / leaderboard cache | $350 |
| S3 + CloudFront | 500 GB storage + 2 TB transfer | $200 |
| OpenAI API (GPT-4o) | ~2 M requests/month | $4,000 |
| Google Vision API | ~500 K OCR calls/month | $750 |
| NAT Gateway + Data Transfer | Outbound traffic | $250 |
| Monitoring (Datadog or self-hosted) | Metrics, logs, traces | $400 |
| Misc (Secrets Mgr, Route 53, WAF) | Supporting services | $200 |
| **Total** | | **~$7,800/mo** |

*Costs assume moderate initial traffic (~10 K DAU). LLM costs scale linearly and represent the dominant variable.*

---

## 13. Recommendations & Next Steps

1. **Finalize offline-mode requirements** (GAP-001) — determine scope of local caching before P2.
2. **Conduct accessibility audit** (GAP-002, NFR-USB-003) — engage external WCAG consultancy before beta.
3. **Negotiate LLM SLA & volume pricing** (ASMP-002) — secure committed-use discounts to control the $4 K/mo AI spend.
4. **Define subscription tiers & limits** (ASMP-004) — required before P4 payments module.
5. **Run proof-of-concept** for OCR + math recognition pipeline to validate ≥ 95 % accuracy (FR-SCAN-003) on handwritten input.
6. **Establish content-moderation policy** (ASMP-005, RISK-005) — finalize before any public-facing LLM output.
7. **Plan load test** — simulate 500+ concurrent users (NFR-PERF-004) at the end of P3.

---

## 14. Traceability Matrix

| RA Requirement | DE Section | Design Artifact |
|---------------|-----------|----------------|
| FR-AUTH-001 – 007 | §2.3, §6, §7, §10 | Auth Service, `users`/`sessions` tables, `/auth/*` endpoints |
| FR-SCAN-001 – 010 | §2.3, §5.1, §6, §7 | AI Service, `scan_history` table, `/scan/*` endpoints |
| FR-WRITE-001 – 010 | §2.3, §6, §7 | AI Service, `writing_projects` table, `/write/*` endpoints |
| FR-LANG-001 – 007 | §2.3, §6, §7 | AI Service, `/language/*` endpoints |
| FR-REW-001 – 007 | §2.3, §6, §7 | AI Service, `rewrite_history` table, `/rewrite` endpoint |
| FR-CODE-001 – 009 | §2.3, §5.2, §6, §7 | Code Service, `code_sessions` table, `/code/*` endpoints |
| FR-TRIV-001 – 007 | §2.3, §6, §7 | Trivia Service, `trivia_*` tables, `/trivia/*` WebSocket |
| TC-001 | §3.1 | Flutter 3.22 targeting iOS 15+ / Android 10+ |
| TC-002 | §7 | REST + WebSocket endpoints |
| TC-003 | §3.2, §9 (TR-006) | OpenAI / Azure OpenAI with abstraction layer |
| TC-004 | §3.2, §5.1 | Google Cloud Vision integration |
| TC-005 | §3.3, §6 | PostgreSQL 16 on RDS with read replicas |
| TC-006 | §3.3, §4 | S3 + CloudFront |
| TC-007 | §3.2, §5.2, §9 (TR-003), §10 | gVisor ephemeral containers |
| TC-008 | §4, §6 | Stripe + Apple IAP + Google Play Billing, `subscriptions` table |
| NFR-SEC-001 – 005 | §10 | TLS, bcrypt, rate limiting, AES-256, OWASP scans |
| NFR-PERF-001 – 004 | §5.1, §11, §12 | SLO dashboards, EKS auto-scaling |
| NFR-AVL-001 – 003 | §4, §11 | Multi-AZ EKS, RDS failover, PagerDuty alerts |
| NFR-USB-001 – 003 | §3.1, §13 | Flutter widgets, WCAG audit recommendation |
| NFR-MNT-001 – 003 | §4, §7 | GitHub Actions CI, OpenAPI auto-gen, ArgoCD rolling deploys |
| RISK-001 – 006 | §9 | Mapped to TR-001 – TR-005 with mitigations |
| GAP-001, GAP-002 | §13 | Recommendations #1 and #2 |
