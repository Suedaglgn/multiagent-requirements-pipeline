# Design & Estimation (DE) Document  
**Source:** Approved Requirements Analysis – Paylocity Employee & Supervisor Application  

---  

## 1. Executive Summary  

This document provides a comprehensive design and cost‑estimation plan for the **Paylocity Employee & Supervisor Application**. It translates the approved Requirements Analysis (RA) into actionable architecture, technology choices, implementation effort, and financial outlook. The design satisfies every functional requirement (FR‑*), non‑functional requirement (NFR‑*), technical constraint (TC‑*), edge case (EDGE‑*), and assumption (ASS‑*) while preserving traceability across the entire project lifecycle.

---  

## 2. System Architecture  

### 2.1 Pattern Justification  

| Pattern | Rationale (RA reference) |
|---------|--------------------------|
| **Event‑Driven Architecture (EDA)** | Supports real‑time push notifications (FR‑PUSH‑*, FR‑CHAT‑*, FR‑ORG‑*) and asynchronous wage‑approval workflows (FR‑WAGE‑*). Aligns with NFR‑SEC‑002 (secure storage) and TC‑INT‑006 (hosted push). |
| **Micro‑services (API‑centric)** | Isolates concerns per module (Auth, Payroll, Schedule, Chat, Org). Supports TA‑INT‑001 (API‑v2 only) and TFR‑INT‑002 (scalable). |
| **CQRS (Command‑Query Responsibility Segregation)** | Allows high‑throughput read operations (FR‑PAY‑001, FR‑SCHED‑001) without impacting write latency (FR‑PAY‑003, FR‑SCHED‑003). |
| **CQRS + Event Sourcing** | Enables audit logging (FR‑SUP‑005) and reliable replay for org‑chart sync (TFR‑INT‑004). |
| **API‑Gateway** | Centralizes auth (FR‑AUTH‑* ), rate‑limiting, and throttling (NFR‑AVAIL‑001). |

### 2.2 ASCII Diagram  

```
+-------------------+          +-------------------+          +-------------------+
|   Front‑End UI   |  HTTPS   |   API Gateway    |  REST   |   Microservices   |
| (React Native)   | <------> | (Auth, Rate‑limit)| <------> |   (Auth, Payroll, |
+-------------------+          +-------------------+          |   Schedule, Chat)|
                                          |          |          |
                                          +---------+---------+---------+
                                                |          |
                                                |   PostgreSQL   |   S3 (Logs)
                                                |   (v14)        |   (Event store)
                                                +---------+---------+---------+
                                                            |
                                                            v
                                   +-------------------+   +-------------------+
                           CloudWatch   |   |  SNS/SQS          |   |  Event Store (v1)
                           (v2022)    |   |  (v2023)       |   |  (v2022)
                           +-------------------+   +-------------------+
```

### 2.3 Component List  

| Component | Tech Stack | RA‑linked Feature |
|-----------|------------|-------------------|
| Mobile Client | React Native (v0.72), TypeScript | FR‑AUTH‑001, FR‑CLOCK‑001, FR‑CHAT‑001 |
| API Gateway | AWS API Gateway (v1.0) | FR‑AUTH‑001, FR‑AUTH‑002, FR‑AUTH‑003 |
| Auth Service | Node.js (v20), JWT (v5) | FR‑AUTH‑001‑007, NFR‑SEC‑001 |
| Payroll Service | Node.js (v20), Express (v4) | FR‑PAY‑001‑007, NFR‑SEC‑002 |
| Schedule Service | Node.js (v20), GraphQL (v14) | FR‑SCHED‑001‑006, TFR‑INT‑004 |
| Chat Service | Node.js (v20), WebSocket (v14) | FR‑CHAT‑001‑005 |
| Org‑Chart Service | Node.js (v20), Redis (v7) | FR‑ORG‑001‑004 |
| Notification Service | AWS SNS (v2023) + S3 (v2) | FR‑PUSH‑001‑005 |
| CI/CD Pipeline | GitHub Actions (v3) | All modules |
| Monitoring | CloudWatch (v2022), Grafana (v10) | NFR‑MNT‑001 |

---  

## 3. Technology Stack  

| Layer | Technology | Version | Justification (RA reference) |
|-------|------------|---------|------------------------------|
| **Frontend** | React Native (iOS/Android) | 0.72.0 | Meets FR‑AUTH‑001 (biometric SDK compatibility) and ASS‑002 (smartphone biometric support). |
| | TypeScript | 5.4 | Guarantees type safety for FR‑MGT‑* (SRP‑001). |
| **Backend** | Node.js (v20) | 20.x | Core of all microservices; aligns with ASS‑001 (API v2). |
| | Express (v4) | 4.18.2 | REST & GraphQL endpoints; supports TC‑INT‑001. |
| | GraphQL (v14) | 14.x | Enables FR‑PAY‑002 (historical pay) with sub‑500 ms latency (NFR‑PERF‑001). |
| | Redis (v7) | 7.13.0 | Session store, org‑chart sync (TFR‑INT‑004). |
| **Database** | PostgreSQL (v14) | 14.4 | ACID compliance for FR‑PAY‑001, FR‑SCHED‑001; NFR‑AVAIL‑001. |
| | Event Store (S3 + EventBridge) | v2022 | Auditable logging (FR‑SUP‑005). |
| **Cloud Services** | AWS SNS / SQS | 2023 | Push notifications (FR‑PUSH‑001) and decoupled event handling. |
| | CloudWatch (v2022) | 2022.04 | NFR‑MNT‑001, NFR‑SEC‑001 (monitoring). |
| **CI/CD** | GitHub Actions (v3) | 3.9.5 | Automated testing, linting; TFR‑INT‑002 (scalable). |
| **Infrastructure** | Terraform (v1.6) | 1.6.0 | IaC for reproducible environments. |

---  

## 4. Infrastructure & Deployment  

| Service | AWS Region | Role | RA/TF Reference |
|---------|------------|------|-----------------|
| EC2 (API & Auth) | us-east-1 | Compute | TFR‑INT‑002 |
| RDS PostgreSQL | us-east-1 | Persistence | NFR‑AVAIL‑001 |
| S3 (Logs, Event store) | us-east-1 | Object store | TFR‑INT‑006 |
| SQS / SNS | us-east-1 | Messaging | FR‑PUSH‑* |
| IAM Roles | us-east-1 | Least‑privilege | NFR‑SEC‑001 |
| GitHub Actions | us-east-1 | CI/CD | TFR‑INT‑002 |

**CI/CD Pipeline**  
1. **Commit →** GitHub Actions triggers `build-test-deploy` workflow.  
2. **Build** – Transpiles TS to JS, runs unit tests (≥80 % coverage, NFR‑MNT‑001).  
3. **Test** – Unit, integration, contract tests (API‑v2).  
4. **Deploy** – Blue‑green deploy to staging (TFR‑INT‑004); promote to prod after smoke test.  

**Environments**  
- **Dev** – Staging infra, isolated DB schema.  
- **Staging** – Full replica of prod, used for UI/UX validation (FR‑PAY‑001).  
- **Prod** – Production services, monitored via CloudWatch.  

---  

## 5. Data Flows for Key Use Cases  

### 5.1 Biometric Clock‑In (FR‑CLOCK‑001)  

| Step | Action | RA Reference | Data Flow |
|------|--------|--------------|-----------|
| 1 | User opens app, taps **Biometric Login** button. | FR‑AUTH‑001 | Mobile SDK triggers `BiometricAuth` event. |
| 2 | SDK generates **Secure Token** (AES‑256 encrypted) and **Device ID**. | NFR‑SEC‑002 | Token stored in memory; never written to disk. |
| 3 | App sends **POST /auth/token** to API Gateway (ENC). | FR‑AUTH‑003, NFR‑SEC‑001 | Payload: `{token:..., deviceId:...}`; TLS 1.3. |
| 4 | API Gateway validates token, creates **session JWT**. | FR‑AUTH‑003 | Returns JWT + session ID. |
| 5 | Backend **Auth Service** writes session to Redis (TTL 15 min). | FR‑AUTH‑004, NFR‑AVAIL‑001 | Redis entry `session:{id}` → expiry. |
| 6 | Frontend proceeds to Clock‑In request (FR‑CLOCK‑001). | FR‑CLOCK‑001 | Sends `{sessionId, deviceId}` → Auth Service updates DB. |
| 7 | On clock‑out, system checks session validity; rejects if >5 min later (FR‑CLOCK‑004). | FR‑CLOCK‑004, NFR‑AVAIL‑001 | Reject with 403, logs error (NFR‑SEC‑002). |

### 5.2 Early Wage Request Approval (FR‑WAGE‑001‑004)  

| Step | Action | RA Reference | Data Flow |
|------|--------|--------------|-----------|
| 1 | Employee submits early wage request (≤50 %). | FR‑WAGE‑001 | UI → Payroll Service (GraphQL). |
| 2 | Payroll Service writes request to **Event Queue** (SQS). | FR‑WAGE‑001, TFR‑INT‑007 | Event: `{requestId, userId, amount}` |
| 3 | Event triggers **Approval Workflow** (Lambda). | FR‑WAGE‑004 | Lambda reads request, checks HR approval status (HR API). |
| 4 | If approved, Lambda writes **Wage Credit** event; Payroll Service deducts from future payroll. | FR‑WAGE‑002‑005, NFR‑SEC‑002 | Wage credit stored in DB, encrypted. |
| 5 | Push notification sent via SNS (FR‑PUSH‑001). | FR‑PUSH‑001 | Payload: `{type:"wage_approved", amount}` |
| 6 | If denied, system logs denial reason (FR‑WAGE‑005) and marks request as stale. | FR‑WAGE‑005, NFR‑AVAIL‑001 | No credit, event discarded. |

---  

## 6. Database Schema Overview  

| Table | Primary Key | Key Fields | Description | RA/TF |
|-------|-------------|------------|-------------|------|
| `users` | `user_id` | `email`, `role` | Employee & supervisor profiles. | FR‑DIR‑002, FR‑AUTH‑002 |
| `sessions` | `session_id` | `user_id`, `expires_at` | JWT‑based auth. | FR‑AUTH‑004 |
| `paychecks` | `paycheck_id` | `user_id`, `period_id` | Gross/Deduction/Net layout. | FR‑PAY‑001, FR‑PAY‑002 |
| `wage_requests` | `request_id` | `user_id`, `amount`, `status` | Early‑wage pipeline. | FR‑WAGE‑001‑004 |
| `timesheets` | `timesheet_id` | `user_id`, `shift_id` | Shift & overtime detection. | FR‑SCHED‑001‑005 |
| `org_chart` | `org_id` | `manager_id`, `employee_id` | Hierarchical data. | FR‑ORG‑001‑004 |
| `messages` | `msg_id` | `sender_id`, `recipient_id` | Chat store. | FR‑MSG‑001‑005 |
| `approval_logs` | `log_id` | `request_id`, `approver_id` | Audit trail. | FR‑SUP‑005, NFR‑SEC‑002 |
| `push_events` | `event_id` | `type`, `user_id` | Sent notification payload. | FR‑PUSH‑001‑005 |

All tables use **AES‑256** column‑level encryption for sensitive data (NFR‑SEC‑002).  

---  

## 7. API Surface Overview  

| Endpoint | Method | Description | RA / NFR | TF |
|----------|--------|-------------|----------|----|
| `/auth/token` | POST | Biometric or PIN login; returns JWT. | FR‑AUTH‑001‑003, NFR‑SEC‑001 | TC‑INT‑003 |
| `/api/v2/paycheck` | GET | Current paycheck view. | FR‑PAY‑001, NFR‑PERF‑001 | TF‑INT‑001 |
| `/api/v2/paycheck/history` | GET | Filterable pay history. | FR‑PAY‑002 | TF‑INT‑001 |
| `/api/v2/wage/early` | POST | Submit early‑wage request. | FR‑WAGE‑001, TF‑INT‑007 | TF‑INT‑002 |
| `/api/v2/wage/approval` | GET/POST | Supervisor view & approve/reject. | FR‑WAGE‑002‑005, TFR‑INT‑004 | TF‑INT‑002 |
| `/api/v2/clock` | POST | Clock‑in/out (biometric/PIN). | FR‑CLOCK‑001‑003, TFR‑INT‑003 | TF‑INT‑003 |
| `/api/v2/chat` | POST | Send/receive messages. | FR‑MSG‑001‑005, NFR‑SEC‑002 | TF‑INT‑005 |
| `/api/v2/msg/export` | GET | Export chat log (HR audit). | FR‑MSG‑005, NFR‑SEC‑002 | TF‑INT‑006 |
| `/api/v2/schedule` | GET/POST | View/edit shifts. | FR‑SCHED‑001‑002‑003 | TF‑INT‑002 |
| `/api/v2/org` | GET | Org chart (filtered). | FR‑ORG‑001‑002‑004 | TF‑INT‑004 |
| `/api/v2/push` | POST | Publish event to SNS (fallback). | FR‑PUSH‑* | TF‑INT‑006 |

All endpoints use **REST v2** (TC‑INT‑001) and enforce **role‑based access** (FR‑SUP‑001‑002).  

---  

## 8. Development Effort Estimation  

### 8.1 Module T‑Shirt Sizing  

| Module | Size | RA / NFR Impact |
|--------|------|-----------------|
| Auth (biometric, fallback) | S | FR‑AUTH‑* |
| Payroll (early wage) | M | FR‑PAY‑* |
| Schedule & Timesheet | M | FR‑SCHED‑* |
| Chat & Messaging | M | FR‑MSG‑* |
| Org‑Chart & Directory | M | FR‑DIR‑* |
| Supervisor Admin | S | FR‑SUP‑* |
| Notification & Push | S | FR‑PUSH‑* |
| UI/UX (Dashboard) | M | FR‑PAY‑001, FR‑SCHED‑001 |

### 8.2 Phased Delivery  

| Phase | Deliverables | Approx. Effort | RA Coverage |
|-------|--------------|----------------|-------------|
| **Phase 1** | Auth service, UI login, token management | 4 person‑weeks | FR‑AUTH‑* |
| **Phase 2** | Payroll micro‑service, UI paycheck view | 6 person‑weeks | FR‑PAY‑* |
| **Phase 3** | Schedule & Timesheet service, UI shift editor | 6 person‑weeks | FR‑SCHED‑* |
| **Phase 4** | Chat, Org‑Chart, Directory sync | 8 person‑weeks | FR‑MSG‑*, FR‑DIR‑*, FR‑ORG‑* |
| **Phase 5** | Supervisor admin, Approval workflow, Push notifications | 5 person‑weeks | FR‑SUP‑*, FR‑PUSH‑* |

### 8.3 Team Composition  

| Role | Count | Skill Set |
|------|-------|-----------|
| Front‑end Engineer (React Native) | 2 | TS, Expo, Biometric SDK |
| Backend Engineer (Node.js) | 2 | Express, GraphQL, JWT |
| DevOps / CI‑CD | 1 | Terraform, AWS, GitHub Actions |
| QA Engineer | 1 | Unit, integration, performance testing |
| Product Owner | 1 | RA & NFR tracking |

---  

## 9. Technical Risks & Mitigation  

| Risk ID | Description | Impact | Mitigation |
|---------|-------------|--------|------------|
| **TECH‑001** | Biometric SDK incompatibility (Android 8+, iOS 12+) | Medium | Use ASS‑002; provide fallback PIN; unit test all auth flows. |
| **TECH‑002** | High concurrency on API Gateway (10 k users) | High | Auto‑scale API GW, enforce NFR‑AVAIL‑001, load‑test (TFR‑INT‑002). |
| **TECH‑003** | Database lock during massive payroll batch | Medium | Implement read‑replicas, use serde‑lock; schedule batch after business hours. |
| **TECH‑004** | Event‑store latency causing delayed push | Low | Use SQS (max 2‑min), retry, DLQ with alert (NFR‑SEC‑002). |
| **TECH‑005** | Role escalation bugs (supervisor vs employee) | High | Enforce RBAC via API‑Gateway policies; unit test all access checks. |

---  

## 10. Security Architecture  

| Layer | Controls (RA/TF) | Enforcement |
|-------|------------------|-------------|
| **Transport** | NFR‑SEC‑001 (TLS 1.3) | API‑Gateway enforces TLS 1.3; HSTS header. |
| **Storage** | NFR‑SEC‑002 (AES‑256) | Column‑level encryption on PII; column‑level policies. |
| **Authentication** | FR‑AUTH‑* | JWT signed with RSA‑2048; token revoked via SNS. |
| **Authorization** | FR‑AUTH‑002, FR‑SUP‑* | RBAC middleware (policy engine). |
| **Audit** | FR‑SUP‑005, TFR‑INT‑004 | Event store immutable; signed with SHA‑256. |
| **Monitoring** | NFR‑SEC‑002 | CloudWatch anomaly detection; SOC alert on breach. |

---  

## 11. Monitoring & Observability  

| Metric | Source | Threshold | RA / NFR |
|--------|--------|-----------|----------|
| API latency (p95) | CloudWatch | ≤200 ms | NFR‑PERF‑001 |
| 99th‑pct error rate | CloudWatch | <0.5 % | NFR‑SEC‑001 |
| DB connection pool usage | RDS | <80 % | NFR‑AVAIL‑001 |
| Auth failures | Auth Service | >10 min | TFR‑INT‑003 |
| Event lag (SQS) | EventBridge | >2 min | TFR‑INT‑004 |
| Uptime | CloudWatch | ≥99.95 % | NFR‑AVAIL‑001 |

Dashboard: Grafana with alerts to Slack (SNS).  

---  

## 12. Cost Estimation (Monthly USD)  

| Item | Monthly Cost (USD) | RA / NFR Impact |
|------|-------------------|-----------------|
| EC2 (API GW, Auth, Payroll) | $300 | TFR‑INT‑002 |
| RDS PostgreSQL | $150 | NFR‑AVAIL‑001 |
| S3 (Logs, Event store) | $40 | TFR‑INT‑006 |
| SQS / SNS | $30 | FR‑PUSH‑* |
| CloudWatch (Metrics, Logs) | $80 | NFR‑MNT‑001 |
| GitHub Actions (CI/CD) | $20 | TFR‑INT‑002 |
| Monitoring (Grafana, Slack) | $25 | NFR‑MNT‑001 |
| **Total** | **$645** | — |

---  

## 13. Recommendations & Next Steps  

1. **Finalize Architecture Diagram** – Review with security team.  
2. **Kick‑off Sprint 0** – Create repo, set up Terraform, CI pipeline.  
3. **Implement Phase 1 (Auth)** – Align with FR‑AUTH‑* and NFR‑SEC‑001.  
4. **Validate Payroll API** – Ensure FR‑PAY‑* meets NFR‑PERF‑001.  
5. **Run Load Test** – 10 k concurrent users (TFR‑INT‑002).  

---  

## 14. Traceability Matrix  

| FR ID | NFR ID(s) | TC ID(s) |
|-------|-----------|----------|
| FR‑AUTH‑001 | NFR‑SEC‑001, NFR‑SEC‑002 | TC‑INT‑003 |
| FR‑AUTH‑002 | NFR‑SEC‑001 | TC‑INT‑003 |
| FR‑AUTH‑003 | NFR‑SEC‑002 | TC‑INT‑003 |
| FR‑AUTH‑004 | NFR‑AVAIL‑001 | TC‑INT‑001 |
| FR‑PAY‑001 | NFR‑PERF‑001, NFR‑AVAIL‑001 | TC‑INT‑001 |
| FR‑PAY‑002 | NFR‑SEC‑002 | TFR‑INT‑004 |
| FR‑PAY‑003 | NFR‑SEC‑002 | TFR‑INT‑006 |
| FR‑CLOCK‑001 | NFR‑SEC‑001 | TFR‑INT‑003 |
| FR‑MSG‑001 | NFR‑SEC‑002, NFR‑USR‑001 | TFR‑INT‑005 |
| FR‑TASK‑001 | NFR‑PERF‑001 | TFR‑INT‑001 |
| FR‑DIR‑001 | NFR‑AVAIL‑001 | TFR‑INT‑004 |
| FR‑COMM‑001 | NFR‑SEC‑002, NFR‑USR‑001 | TFR‑INT‑006 |
| FR‑WAGE‑001 | NFR‑AVAIL‑001 | TFR‑INT‑007 |
| FR‑SCHED‑001 | NFR‑SEC‑002 | TFR‑INT‑002 |
| FR‑ORG‑001 | NFR‑AVAIL‑001 | TFR‑INT‑004 |
| FR‑CHAT‑001 | NFR‑SEC‑002 | TFR‑INT‑006 |
| FR‑PUSH‑001 | NFR‑SEC‑002, NFR‑PUSH‑005 | TFR‑INT‑006 |
| FR‑SUP‑001 | NFR‑SEC‑002 | TFR‑INT‑003 |
| FR‑SUP‑003 | NFR‑SEC‑002 | TFR‑INT‑003 |
| FR‑SUP‑005 | NFR‑SEC‑002 | TFR‑INT‑004 |
| FR‑SUP‑006 | NFR‑SEC‑002 | TFR‑INT‑003 |

---  

**Prepared by:** Design & Estimation Team  
**Date:** 2025‑09‑29  

---  

*This document constitutes the definitive Design & Estimation plan for the Paylocity Employee & Supervisor Application, fully traceable to the approved Requirements Analysis.*