# Design & Estimation Document — PictureThis Plant Identification App

## 1. Executive Summary

This document translates the approved Requirements Analysis (RA_3) for **PictureThis** into a concrete system design, technology selection, infrastructure plan, and effort/cost estimation. PictureThis is a cross-platform mobile app offering AI-powered plant identification (FR-IDEN-*), disease diagnosis (FR-DIAG-*), care reminders (FR-CARE-*), expert consultation (FR-EXPT-*), toxicity warnings (FR-TOXI-*), collection management (FR-COLL-*), and personalized recommendations (FR-RECO-*). The architecture is designed around GPU-accelerated ML inference (TC-003), real-time WebSocket chat (TC-005), and a scalable cloud backend targeting 99.9% uptime (NFR-AVL-001) with sub-3-second identification latency (NFR-PERF-001).

---

## 2. System Architecture

### 2.1 Pattern Justification

A **microservices architecture** is chosen to allow independent scaling of the AI inference services (PIE/DDE per TC-003) separately from CRUD and chat workloads (NFR-AVL-002). An API Gateway provides a single entry point enforcing auth (FR-AUTH-005), rate limiting (FR-AUTH-003 guest caps), and TLS termination (NFR-SEC-001).

### 2.2 Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────────┐
│                     Mobile Client (iOS / Android)                    │
│  React Native · Camera (TC-002) · Light Sensor (TC-006) · Offline Q │
└──────────────┬──────────────────────────────────┬────────────────────┘
               │  HTTPS / WSS                     │  Push (TC-004)
               ▼                                  ▼
        ┌─────────────┐                  ┌──────────────────┐
        │ API Gateway  │                  │ FCM / APNs       │
        │ (Kong)       │                  └──────────────────┘
        └──┬───┬───┬───┘
           │   │   │
     ┌─────┘   │   └──────────────┐
     ▼         ▼                  ▼
┌─────────┐ ┌───────────┐  ┌────────────┐
│ Auth    │ │ Core API  │  │ Chat Svc   │──► Redis Pub/Sub
│ Service │ │ (REST)    │  │ (WebSocket)│
└────┬────┘ └─────┬─────┘  └────────────┘
     │            │
     │   ┌────────┼──────────┐
     │   ▼        ▼          ▼
     │ ┌──────┐ ┌──────┐ ┌────────────┐
     │ │ PIE  │ │ DDE  │ │ Recommend. │
     │ │ (GPU)│ │ (GPU)│ │ Service    │
     │ └──────┘ └──────┘ └────────────┘
     │            │
     ▼            ▼
┌──────────┐ ┌──────────┐ ┌───────────────┐
│PostgreSQL│ │   S3     │ │ Elasticsearch │
│ (RDS)    │ │ (Images) │ │ (Plant Meta)  │
└──────────┘ └──────────┘ └───────────────┘
```

### 2.3 Component List

| Component | Responsibility | RA References |
|---|---|---|
| Mobile Client | UI, camera capture, light meter, offline queue, push notifications | FR-IDEN-001/002/007, FR-CARE-007/008, TC-002/004/006 |
| API Gateway (Kong) | Routing, rate limiting, TLS, JWT validation | NFR-SEC-001/003, FR-AUTH-003/005 |
| Auth Service | Registration, OAuth, JWT issuance, password reset, account deletion | FR-AUTH-001–007 |
| Core API | Plant collection CRUD, care reminders, diagnosis history, recommendations | FR-COLL-*, FR-CARE-*, FR-RECO-*, FR-DIAG-007 |
| PIE (Plant Identification Engine) | Species classification inference | FR-IDEN-003/004/008/010 |
| DDE (Disease Diagnosis Engine) | Disease classification & severity inference | FR-DIAG-003/004 |
| Chat Service | WebSocket real-time messaging, escalation | FR-EXPT-001–007, TC-005 |
| Recommendation Service | Filtering & ranking by skill, space, climate, pet-safety | FR-RECO-001–006 |
| Notification Worker | Scheduling & dispatching push notifications | FR-CARE-002–007, TC-004 |

---

## 3. Technology Stack

### 3.1 Frontend

| Technology | Version | Justification |
|---|---|---|
| React Native | 0.76 | Cross-platform (TC-001 iOS 15+ / Android 10+), single codebase |
| TypeScript | 5.5 | Type safety, IDE tooling |
| React Navigation | 7.x | Native-feel navigation |
| react-native-camera | 4.x | Camera integration (TC-002) |
| Zustand | 5.x | Lightweight state management |
| Socket.io Client | 4.x | WebSocket chat (TC-005) |

### 3.2 Backend

| Technology | Version | Justification |
|---|---|---|
| Node.js | 22 LTS | Async I/O for REST + WebSocket workloads |
| NestJS | 10.x | Modular microservice framework, built-in guards for JWT (FR-AUTH-005) |
| Python / FastAPI | 3.12 / 0.115 | ML inference services (PIE/DDE), GPU-optimized (TC-003) |
| PyTorch | 2.4 | Model serving for 17 K species (FR-IDEN-003) and 500 diseases (FR-DIAG-003) |
| Socket.io | 4.x | WebSocket server for expert chat (TC-005) |
| Bull (Redis) | 5.x | Job queue for reminders, offline queue processing (FR-IDEN-007, FR-CARE-*) |

### 3.3 Database & Storage

| Technology | Version | Justification |
|---|---|---|
| PostgreSQL (RDS) | 16 | Relational data; users, collections, reminders (TC-008) |
| Elasticsearch | 8.x | Full-text search for plant metadata, species lookup (TC-008) |
| Redis | 7.x | Session cache, pub/sub for chat, rate-limit counters |
| AWS S3 | — | Image storage with 24-month lifecycle (TC-007) |

---

## 4. Infrastructure & Deployment

| Aspect | Choice | RA Reference |
|---|---|---|
| Cloud Provider | AWS | TC-003, TC-007, TC-008 |
| ML Hosting | SageMaker (GPU p3.2xlarge) with auto-scaling | TC-003, NFR-AVL-002 |
| Container Orchestration | EKS (Kubernetes) | NFR-AVL-001/002 |
| CI/CD | GitHub Actions → ECR → EKS (Helm) | NFR-MNT-003 |
| Environments | dev, staging, production | NFR-MNT-003 |
| CDN | CloudFront | NFR-PERF-003 |
| Secrets | AWS Secrets Manager | NFR-SEC-002/003 |
| IaC | Terraform | Reproducibility |

---

## 5. Data Flows

### 5.1 Plant Identification (UC-001, FR-IDEN-001–006)

1. User captures photo → client compresses to ≤ 2 MB (NFR-PERF-004).
2. Client uploads image to API Gateway → Auth validated (JWT).
3. Core API stores image in S3, sends inference request to PIE.
4. PIE returns top-3 species with confidence scores (FR-IDEN-004).
5. Core API enriches results with Elasticsearch plant metadata (FR-IDEN-005).
6. Toxicity flag checked against toxic plant DB (FR-TOXI-001/002).
7. Results returned to client; identification persisted in PostgreSQL (FR-IDEN-006).

### 5.2 Expert Consultation (UC-004, FR-EXPT-001–006)

1. User opens Expert tab → Core API returns available experts (FR-EXPT-004).
2. User selects expert → Chat Service opens WebSocket channel.
3. Messages & photos relayed via Redis Pub/Sub (FR-EXPT-002).
4. If unresolved, escalation routes to senior specialist (FR-EXPT-007).
5. On close, user prompted to rate (FR-EXPT-006); transcript stored for 12 months (FR-EXPT-005).

---

## 6. Database Schema Overview

| Table | Key Columns | RA Reference |
|---|---|---|
| `users` | id, email, password_hash, display_name, avatar_url, experience_level, location, created_at | FR-AUTH-001/006 |
| `oauth_accounts` | id, user_id, provider, provider_uid | FR-AUTH-002 |
| `identifications` | id, user_id, image_url, species_results (JSONB), location, created_at | FR-IDEN-004/006 |
| `diagnoses` | id, user_id, plant_id, image_url, disease_results (JSONB), severity, created_at | FR-DIAG-003/004/007 |
| `plants` (collection) | id, user_id, species_id, nickname, location_tag, health_status, archived | FR-COLL-001–006 |
| `wishlist` | id, user_id, species_id, notes, price_range | FR-COLL-004 |
| `reminders` | id, plant_id, type (water/fert/mist/clean/repot), cron_expr, quiet_start, quiet_end | FR-CARE-002–007 |
| `chat_sessions` | id, user_id, expert_id, status, escalated_to, created_at | FR-EXPT-001/005/007 |
| `chat_messages` | id, session_id, sender, body, image_url, created_at | FR-EXPT-002 |
| `expert_ratings` | id, session_id, stars, feedback_text | FR-EXPT-006 |
| `toxic_plants` | id, species_id, toxicity_level, affected_groups, toxic_parts | FR-TOXI-002/004 |
| `light_readings` | id, plant_id, lux, classification, recorded_at | FR-CARE-008/009 |

---

## 7. API Surface Overview

| Method | Endpoint | Description | RA Reference |
|---|---|---|---|
| POST | `/auth/register` | Email registration | FR-AUTH-001 |
| POST | `/auth/oauth` | Social login | FR-AUTH-002 |
| POST | `/auth/reset-password` | Password reset | FR-AUTH-004 |
| DELETE | `/auth/account` | Account deletion | FR-AUTH-007 |
| POST | `/identify` | Submit image for identification | FR-IDEN-001/002 |
| GET | `/identify/:id` | Get identification result | FR-IDEN-004/005 |
| GET | `/identify/history` | User identification history | FR-IDEN-006 |
| POST | `/diagnose` | Submit image for disease diagnosis | FR-DIAG-001/002 |
| GET | `/plants` | List user's collection | FR-COLL-002/006 |
| POST | `/plants` | Add plant to collection | FR-COLL-001 |
| GET | `/plants/:id` | Plant detail + care + diagnosis history | FR-COLL-003 |
| POST | `/reminders` | Create care reminder | FR-CARE-002–006 |
| GET | `/recommendations` | Get filtered plant recommendations | FR-RECO-001–005 |
| WS | `/chat` | Expert consultation (WebSocket) | FR-EXPT-001–003 |
| POST | `/chat/:sessionId/rate` | Rate expert | FR-EXPT-006 |
| POST | `/light-meter` | Log light reading | FR-CARE-008 |

---

## 8. Development Effort Estimation

### 8.1 Module Sizing

| Module | Size | Effort (weeks) | RA References |
|---|---|---|---|
| Auth & User Mgmt | M | 3 | FR-AUTH-* |
| Plant Identification | XL | 6 | FR-IDEN-*, TC-003 |
| Disease Diagnosis | L | 5 | FR-DIAG-* |
| Care & Reminders | M | 3 | FR-CARE-* |
| Expert Chat | L | 5 | FR-EXPT-*, TC-005 |
| Toxicity System | S | 2 | FR-TOXI-* |
| Collection Mgmt | M | 3 | FR-COLL-* |
| Recommendations | M | 3 | FR-RECO-* |
| Infrastructure/DevOps | L | 4 | NFR-AVL-*, NFR-MNT-* |
| Mobile Shell & UX | L | 5 | TC-001/002/006, NFR-USB-* |
| **Total** | — | **39 weeks** | — |

### 8.2 Phased Delivery

| Phase | Duration | Deliverables |
|---|---|---|
| Phase 1 — MVP | Weeks 1–16 | Auth, Identification, Collection, Care Reminders, Toxicity |
| Phase 2 — Enrichment | Weeks 17–28 | Disease Diagnosis, Expert Chat, Recommendations |
| Phase 3 — Polish | Weeks 29–39 | Localization (NFR-USB-002), Offline lite model (RISK-002), Export (FR-COLL-007), Household scan (FR-TOXI-005) |

### 8.3 Team Composition

| Role | Count |
|---|---|
| React Native Developer | 2 |
| Backend Engineer (Node.js) | 2 |
| ML Engineer (Python) | 2 |
| DevOps / SRE | 1 |
| QA Engineer | 2 |
| UX/UI Designer | 1 |
| Product Manager | 1 |
| **Total** | **11** |

---

## 9. Technical Risks & Mitigation

| Risk ID | Risk | Impact | Mitigation | RA Ref |
|---|---|---|---|---|
| TR-001 | PIE accuracy < 98% at launch | Misidentification of toxic species (RISK-001/006) | Staged rollout; confidence threshold gating; human review queue | FR-IDEN-003, FR-IDEN-009 |
| TR-002 | GPU inference cost overrun | Budget breach | Batch inference, model quantization (INT8), spot instances | TC-003 |
| TR-003 | Expert chat SLA breach at peak | User churn (RISK-003) | AI triage bot, elastic expert pool, queue ETA display | FR-EXPT-003 |
| TR-004 | EXIF data leak | Privacy violation (RISK-005) | Server-side EXIF stripping on upload | NFR-SEC-005 |
| TR-005 | Offline queue data loss | Lost identification requests | SQLite local persistence + retry with exponential backoff | FR-IDEN-007 |

---

## 10. Security Architecture

| Layer | Measure | RA Reference |
|---|---|---|
| Transport | TLS 1.3, HSTS, certificate pinning on mobile | NFR-SEC-001 |
| Authentication | JWT (1 h) + refresh token (30 d), bcrypt password hashing | NFR-SEC-003, FR-AUTH-001/005 |
| Authorization | RBAC: guest (3 IDs/day), user, expert, admin | FR-AUTH-003 |
| Data at Rest | AES-256 (RDS encryption, S3 SSE-KMS) | NFR-SEC-002 |
| Privacy | EXIF stripping, GDPR data export/deletion endpoints, consent flow | NFR-SEC-005, FR-AUTH-007 |
| App Security | OWASP Mobile Top 10 audit, dependency scanning (Dependabot) | NFR-SEC-004 |

---

## 11. Monitoring & Observability

| Concern | Tool | Details |
|---|---|---|
| APM | Datadog | Distributed tracing across API Gateway → services → ML inference |
| Logging | ELK (CloudWatch → OpenSearch) | Structured JSON logs; 30-day retention |
| Metrics | Prometheus + Grafana | p95 latency (NFR-PERF-001/002), error rates, SageMaker GPU utilization |
| Alerts | PagerDuty | Uptime < 99.9% (NFR-AVL-001), latency > 3 s, error rate > 1% |
| Mobile | Sentry | Crash reporting, ANR tracking (NFR-PERF-003) |
| Model | MLflow | PIE/DDE accuracy drift monitoring (FR-IDEN-010) |

---

## 12. Cost Estimation (Monthly, USD)

| Item | Specification | Monthly Cost |
|---|---|---|
| EKS Cluster (3 nodes m6i.xlarge) | Compute for API, Chat, Workers | $650 |
| SageMaker (2× ml.g4dn.xlarge) | PIE + DDE inference | $1,800 |
| RDS PostgreSQL (db.r6g.large, Multi-AZ) | Primary database | $450 |
| ElastiCache Redis (r6g.large) | Cache + pub/sub | $300 |
| OpenSearch (2× m6g.large.search) | Plant metadata search | $400 |
| S3 + CloudFront | Image storage (est. 5 TB) + CDN | $250 |
| Misc (Secrets Manager, Route53, NAT) | Supporting services | $150 |
| Monitoring (Datadog, Sentry) | APM + crash reporting | $350 |
| **Total** | | **$4,350/mo** |

*Costs assume moderate traffic (~50 K MAU). Auto-scaling may increase ML costs by up to 3× at peak.*

---

## 13. Recommendations & Next Steps

1. **Prototype PIE model** on a labeled benchmark to validate the 98% accuracy target (FR-IDEN-003) before committing to SageMaker infrastructure.
2. **Define monetization tiers** (GAP-001) to inform rate-limiting logic and premium feature gating.
3. **Recruit expert pool** (ASM-003) — at least 20 verified botanists — in parallel with Phase 1 development.
4. **Build admin panel** (GAP-002) as a Phase-2 web app for expert management and content moderation.
5. **Run OWASP Mobile audit** (NFR-SEC-004) at the end of Phase 1 before public beta.
6. **Establish model retraining pipeline** (FR-IDEN-009/010) with quarterly cadence using user-confirmed corrections.
7. **Plan on-device lite model** (RISK-002) for top-500 species to reduce offline dependency.

---

## 14. Traceability Matrix

| RA Requirement | DE Section(s) | Component(s) | Phase |
|---|---|---|---|
| FR-AUTH-001–007 | §2.3, §3.2, §6, §7 | Auth Service | 1 |
| FR-IDEN-001–010 | §2.3, §3.2, §5.1, §6, §7 | Mobile Client, Core API, PIE | 1 |
| FR-DIAG-001–007 | §2.3, §3.2, §5.1, §6, §7 | Mobile Client, Core API, DDE | 2 |
| FR-CARE-001–010 | §2.3, §3.2, §6, §7 | Core API, Notification Worker | 1 |
| FR-EXPT-001–007 | §2.3, §3.2, §5.2, §6, §7 | Chat Service, Redis | 2 |
| FR-TOXI-001–005 | §2.3, §5.1, §6 | Core API, toxic_plants DB | 1 |
| FR-COLL-001–007 | §2.3, §6, §7 | Core API, PostgreSQL | 1 |
| FR-RECO-001–006 | §2.3, §6, §7 | Recommendation Service | 2 |
| NFR-SEC-001–005 | §10 | API Gateway, Auth Service, S3, RDS | 1 |
| NFR-PERF-001–004 | §4, §11 | SageMaker, EKS, CloudFront | 1 |
| NFR-AVL-001–003 | §4, §11 | EKS auto-scaling, SageMaker | 1 |
| NFR-USB-001–003 | §8.2 | Mobile Client | 1–3 |
| NFR-MNT-001–003 | §4 | GitHub Actions, EKS | 1 |
| TC-001–008 | §3, §4 | All | 1 |
