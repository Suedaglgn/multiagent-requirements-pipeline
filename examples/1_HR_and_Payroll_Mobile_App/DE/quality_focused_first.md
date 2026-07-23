# Design & Estimation (DE) – Paylocity Mobile Application  
*Derived from the Approved Requirements Analysis (RA) Document (ID: **RA‑001**)*  

---  

## 1. Executive Summary  

This Design & Estimation document translates the approved **Software Requirements Specification (SRS)** for the Paylocity mobile application (RA‑001) into a concrete design blueprint and quantitative estimate. It covers the end‑to‑end system architecture, technology stack, infrastructure, data & API flows, development effort, risks, security, monitoring, cost, and traceability. The solution is scoped to the **in‑scope** functional & non‑functional requirements (FR‑001 … FR‑005, NFR‑001 … NFR‑001, TC‑001 … TC‑006) and excludes out‑of‑scope items (e.g., third‑party analytics, external payroll).  

---  

## 2. System Architecture  

### 2.1 Pattern Justification  

| Pattern | RA‑Referenced Requirement | Benefit | Rationale |
|---------|---------------------------|---------|-----------|
| **Hexagonal (Portuguese) Architecture** | FR‑AUTH‑001 (Biometric & Password login), FR‑SCH‑001 (Schedule change) | Decouples UI from business logic | Allows independent updates to RBAC (FR‑AUTH‑004) and scheduling validation (FR‑SCH‑002) without UI changes |
| **Event‑Driven (CQRS)** | FR‑EXP‑002 (Expense approval workflow), FR‑JOUR‑002 (Journal audit) | Scales approval processing and audit logging | Guarantees low latency for push notifications (FR‑NFT‑001) while preserving audit integrity |
| **Circuit Breaker** | FR‑EDG‑006 (Push notification fails) | Prevents cascading failures | Aligns with TC‑005 (Integration) and NFR‑AF‑001 (Authentication Failure) |
| **Strangler Pattern** | FR‑ORG‑002 (Reach‑out), FR‑CHAT‑001 (In‑app chat) | Migrates legacy chat to microservice later | Meets NC‑001 (Compatibility) and TF‑003 (Scalability) |

### 2.2 ASCII Diagram  

```
+-------------------+        +-------------------+        +-------------------+
|   React Native    |  HTTPS |  API Gateway     |  gRPC  |  Microservices    |
|   (Frontend)      |------->|  (Auth, Rate)    |------->|  (Auth, Payroll,  |
|   UI, Push        |        |  (OAuth2)        |        |   Scheduling, …) |
+--------+----------+        +--------+----------+        +--------+----------+
         |                         |                        |
         |                         v                        v
         |               +----------+   +-------------------+
         |               |  RDS (PG|   |  S3 (Static Assets)|
         |               |  8.2)    |   |  (Images, PDFs)   |
         |               +----------+   +-------------------+
         |                         |                        |
         v                         v                        v
   +------+               +----------+          +-----------+
   |  Auth |               |  Payroll |          |  Scheduler|
   +------+               +----------+          +-----------+

[Event Bus]  <--->  [Redis (Pub/Sub)]  <--->  [Audit Log (S3)]

```

### 2.3 Component List  

| Component | RA‑ID(s) | Description |
|-----------|----------|-------------|
| **React Native UI** | FR‑AUTH‑001, FR‑AUTH‑002, FR‑SCH‑001 | Core mobile experience, biometric login, session timeout |
| **Auth Service (Node.js)** | FR‑AUTH‑001, FR‑AUTH‑002, FR‑AUTH‑003, FR‑AUTH‑004, FR‑AUTH‑005 | Handles SSO (FR‑AUTH‑003), RBAC (FR‑AUTH‑004), lockout (FR‑AUTH‑005) |
| **Payroll Service (Node.js)** | FR‑PAY‑001, FR‑PAY‑002, FR‑PAY‑003, FR‑PAY‑004 | Displays net pay, processes earned‑wage advances (FR‑PAY‑003) |
| **Scheduling Service (Node.js)** | FR‑SCH‑001, FR‑SCH‑002, FR‑SCH‑003, FR‑SCH‑004 | Schedule creation, validation (FR‑SCH‑002), swap approval (FR‑SCH‑003) |
| **Chat Service (Node.js)** | FR‑CHAT‑001, FR‑CHAT‑002, FR‑ORG‑002 | In‑app chat, reach‑out, org‑chart view |
| **Expense Service (Node.js)** | FR‑EXP‑001, FR‑EXP‑002, FR‑EXP‑003, FR‑EXP‑004 | Receipt capture, approvals, timeline |
| **Journal Service (Node.js)** | FR‑JOUR‑001, FR‑JOUR‑002 | Supervisor journal entries, audit log |
| **Event Bus / Redis** | FR‑EXP‑002, FR‑JOUR‑002, FR‑EXP‑004, FR‑JOUR‑002 | Event sourcing, real‑time notifications |
| **Audit Log Store (S3)** | FR‑EXP‑004, FR‑JOUR‑002, FR‑EXP‑004 | Immutable logs for compliance |
| **Push Notification Service** | FR‑NFT‑001, FR‑NFT‑002, FR‑NFT‑003, FR‑EDG‑006 | Delivery confirmation, fallback, throttling |
| **API Gateway** | TC‑001, TC‑002, TC‑003, TC‑004, TC‑005, TC‑006 | OAuth2 token validation, rate‑limiting, security |
| **Database (RDS – PostgreSQL 8.2)** | NFR‑AF‑001 (Security), NFR‑MA‑001 (Maintainability) | Stores user, role, session, transaction data |
| **CI/CD (GitHub Actions)** | MF‑001 (Maintainability) | Automated tests, security scans, deployments |

---  

## 3. Technology Stack  

| Layer | Technology | Version | Justification (RA‑Ref) |
|-------|------------|---------|------------------------|
| **Frontend** | React Native (Expo) | 0.73.x | Supports biometric login (FR‑AUTH‑001) and push notifications (FR‑NFT‑001). Compatible with iOS 13+ / Android 9+ (NC‑001). |
| **UI Libraries** | React Navigation 6.x, React Native Paper | 6.x | Improves accessibility, reduces accessibility bugs (NFR‑MA‑001). |
| **Backend** | Node.js (v20 LTS) + Express.js | 20.x | Provides fast API response (<200 ms) for login (NFR‑PER‑002). |
| **Microservices** | NestJS (TypeScript) | 10.x | Enforces strict typing, supports RBAC (FR‑AUTH‑004) and event handling (FR‑EXP‑002). |
| **Database** | PostgreSQL 8.2 (RDS) | 8.2 | AES‑256 encryption at rest (NFR‑NFT‑001), high‑performance queries for timesheet view (FR‑TIME‑005). |
| **Cache / Pub/Sub** | Redis 7.2 | 7.2 | Low‑latency event bus for approval workflows (FR‑EXP‑002). |
| **Storage** | Amazon S3 | 5.0 | Stores PDF pay statements, receipts (FR‑EXP‑001). |
| **CI/CD** | GitHub Actions | 2.42 | Automated unit & integration tests, security linting (MF‑001). |
| **Monitoring** | CloudWatch, Datadog, OpenTelemetry | 2024 | Real‑time metrics, alerts (NFR‑AVA‑001). |
| **Security** | OWASP ZAP, Snyk | latest | Compliance with NFR‑NFT‑001, NFR‑AF‑001. |

---  

## 4. Infrastructure & Deployment  

| Service | RA‑ID(s) | Purpose |
|---------|----------|---------|
| **AWS EC2 / ECS (Fargate)** | TC‑005, TC‑006 | Host microservices, auto‑scale with 100 k users (NFR‑SCA‑001). |
| **RDS PostgreSQL** | NFR‑NFT‑001, NFR‑MA‑001 | Secure data store, versioned backups. |
| **S3** | FR‑EXP‑001, FR‑EXP‑004 | Object storage for receipts, PDFs. |
| **Route 53** | TC‑001 | DNS, failover for API endpoint. |
| **IAM** | FR‑AUTH‑001, FR‑AUTH‑005 | Role‑based access control enforcement. |
| **GitHub Packages** | MF‑001 | Container images, versioned releases. |
| **CI/CD Pipeline** | MF‑001 | Build → Test → Deploy to dev → Test → Deploy to prod. |
| **Environments** | – | **dev** (sandbox, limited users), **staging** (full flow), **prod** (live). |

### Deployment Phases  

| Phase | Duration | Milestone | RA‑Ref |
|-------|----------|-----------|--------|
| **Phase 1 (Month 1‑2)** | 2 mo | Core authentication, login flow, session timeout (FR‑AUTH‑001, FR‑AUTH‑002). | |
| **Phase 2 (Month 3‑5)** | 3 mo | Payroll view, earned‑wage advance (FR‑PAY‑001‑003), scheduling (FR‑SCH‑001‑004). | |
| **Phase 3 (Month 6‑8)** | 3 mo | Chat, org‑chart, expense submission (FR‑ORG‑001, FR‑EXP‑001). | |
| **Phase 4 (Month 9‑12)** | 3 mo | Supervisor journal, audit, final polish, compliance testing. | |

---  

## 5. Data Flows for Key Use Cases  

### 5.1 Clock‑In / Clock‑Out (FR‑TIME‑001, FR‑TIME‑002)  

1. **User opens app** → React Native UI (FR‑AUTH‑001) validates session token (FR‑AUTH‑004).  
2. **Biometric login** → Android/iOS SDK (FR‑AUTH‑005) returns `authToken`.  
3. **Send token to Auth Service** (HTTPS) → `POST /api/auth/login` (RA‑FR‑AUTH‑001).  
4. **Server creates session** (RDS) → `session_id` stored for 15 min (NC‑001).  
5. **Clock‑in request** → `POST /api/time/clockin` (FC‑001).  
6. **Backend writes entry** (RDS `time_entries`) → `event` published to Redis (FR‑EXP‑002).  
7. **Push notification** → Notifier service (FR‑NFT‑001) sends “clocked in”.  

### 5.2 Time‑Off Request (FR‑TIME‑002)  

1. **User creates request** → UI (FR‑AUTH‑001).  
2. **Validate role** (RBAC FR‑AUTH‑004).  
3. **Submit to Scheduler Service** → `POST /api/schedule/timeoff` (RA‑FR‑TIME‑002).  
4. **Scheduler validates** (FR‑SCH‑002) → `event` (Redis).  
5. **Supervisor approval** → Scheduler checks availability → `event` (Redis).  
6. **Notification** → Push to user (FR‑NFT‑001).  
7. **Audit** → Journal entry logged (FR‑JOUR‑002).  

### 5.3 Expense Submission (FR‑EXP‑001)  

1. **User captures receipt** → Camera API (FR‑EXP‑001).  
2. **Upload to S3** → `POST /api/expense/upload` (RA‑FR‑EXP‑001).  
3. **Backend processes image** → extracts data (OCR).  
4. **Expense record created** → DB row `expenses`.  
5. **Event** → `expense_submitted` (Redis) → Notification to supervisor.  
6. **Supervisor approves** → `approve_expense` API (FR‑EXP‑002).  
7. **Push notification** → `FR‑NFT‑001`.  

---  

## 6. Database Schema Overview  

| Table | RA‑ID(s) | Columns (Key) | Purpose |
|-------|----------|---------------|---------|
| **users** | FR‑AUTH‑001, FR‑AUTH‑004 | `user_id PK`, `email`, `role ENUM(EMP,SUPERVISOR)`, `created_at` | Store user credentials, role for RBAC (FR‑AUTH‑004). |
| **sessions** | FR‑AUTH‑002 | `session_id PK`, `user_id FK`, `token`, `expires_at` | Enforce session timeout (FR‑AUTH‑002). |
| **time_entries** | FR‑TIME‑001, FR‑TIME‑005 | `entry_id PK`, `user_id FK`, `clock_type`, `timestamp` | Log clock‑in/out with ±1 s accuracy (FR‑TIME‑001). |
| **expenses** | FR‑EXP‑001, FR‑EXP‑004 | `expense_id PK`, `user_id FK`, `receipt_url`, `amount`, `approved BOOLEAN` | Store expense data, audit trail (FR‑EXP‑004). |
| **time_off_requests** | FR‑TIME‑002, FR‑SCH‑003 | `request_id PK`, `user_id FK`, `supervisor_id FK`, `status`, `created_at` | Manage approved/disallowed time‑off (FR‑TIME‑002). |
| **expense_approvals** | FR‑EXP‑002 | `approval_id PK`, `expense_id FK`, `supervisor_id FK`, `approved_at` | Record approval decision (FR‑EXP‑002). |
| **journal_entries** | FR‑JOUR‑001, FR‑JOUR‑002 | `journal_id PK`, `user_id FK`, `entry_id FK`, `action`, `timestamp` | Audit log for supervisor actions (FR‑JOUR‑002). |
| **push_notifications** | FR‑NFT‑001, FR‑EDG‑006 | `notify_id PK`, `user_id FK`, `content`, `delivered BOOLEAN`, `failed_at` | Delivery status tracking (FR‑NFT‑001). |
| **audit_logs** | FR‑JOUR‑002 | `log_id PK`, `user_id FK`, `event`, `payload` | Immutable storage of all changes (S3). |

---  

## 7. API Surface Overview  

| Endpoint | Method | RA‑ID(s) | Description |
|----------|--------|----------|-------------|
| `POST /api/auth/login` | POST | FR‑AUTH‑001, FR‑AUTH‑003 | SSO / Biometric login; returns JWT token. |
| `GET /api/auth/session` | GET | FR‑AUTH‑002 | Checks token expiration (session timeout). |
| `POST /api/time/clockin` | POST | FR‑TIME‑001 | Records clock‑in. |
| `POST /api/time/clockout` | POST | FR‑TIME‑001 | Records clock‑out. |
| `POST /api/schedule/timeoff` | POST | FR‑TIME‑002, FR‑SCH‑003 | Submits time‑off request. |
| `GET /api/expense/upload` | POST | FR‑EXP‑001 | Uploads receipt (S3). |
| `POST /api/expense/approve` | POST | FR‑EXP‑002 | Supervisor approves expense. |
| `GET /api/expense/report` | GET | FR‑EXP‑004 | Generates PDF statement. |
| `POST /api/schedule/approve` | POST | FR‑SCH‑003 | Approves schedule swap. |
| `POST /api/expense/reject` | POST | FR‑EXP‑002 | Rejects expense. |
| `GET /api/expense/history` | GET | FR‑EXP‑005 | Lists all expenses. |
| `GET /api/journal/entries` | GET | FR‑JOUR‑002 | Audit trail for journal entries. |
| `GET /api/notification/status` | GET | FR‑NFT‑001 | Returns delivery status. |

---  

## 8. Development Effort Estimation  

| Module | T‑shirt Size | RA‑Ref | Phase | Team Composition |
|--------|--------------|--------|-------|-------------------|
| **Core Authentication** | L | FR‑AUTH‑001‑005 | Phase 1 | 2 Frontend, 2 Backend |
| **Payroll UI & Advance** | M | FR‑PAY‑001‑004 | Phase 2 | 2 Frontend, 2 Backend |
| **Scheduling** | M | FR‑SCH‑001‑004 | Phase 2 | 2 Frontend, 2 Backend |
| **Chat & Org Chart** | M | FR‑ORG‑001‑003 | Phase 3 | 2 Frontend, 2 Backend |
| **Expense Management** | L | FR‑EXP‑001‑004 | Phase 3 | 1 Frontend, 2 Backend |
| **Supervisor Journal** | M | FR‑JOUR‑001‑002 | Phase 4 | 1 Frontend, 2 Backend |
| **CI/CD & Security** | S | MF‑001 | Ongoing | 1 DevOps, 1 SecOps |
| **Testing & QA** | S | NFR‑MA‑001 | Ongoing | 1 QA |

**Total effort:** ~ **5 months** of concurrent work (28 person‑days per month).  

---  

## 9. Technical Risks & Mitigation  

| Risk ID | Description | RA‑ID(s) | Mitigation |
|---------|-------------|----------|------------|
| **TC‑005** | High concurrent users cause latency | FR‑TIME‑005, FR‑EXP‑005 | Autoscaling (ECS), Redis buffering, rate‑limit (FR‑NFT‑003). |
| **FR‑EDG‑006** | Push notification fails | FR‑EDG‑006 | Circuit breaker, retry (3×), fallback banner (FR‑NFT‑002). |
| **FR‑AF‑001** | Authentication failure | FR‑AUTH‑002 | Graceful UI fallback, email alert. |
| **FR‑EDG‑008** | Session timeout data loss | FR‑AUTH‑002 | Store session data server‑side, re‑auth prompt. |
| **NC‑001** | Low OS support | FR‑AUTH‑003 | Detect capability, fallback to password. |
| **NFR‑AF‑001** | Data breach | NFR‑NFT‑001 | TLS 1.2+, AES‑256, OWASP ZAP. |
| **NFR‑SCA‑001** | 100 k users latency | NFR‑SCA‑001 | Horizontal scaling, async processing. |
| **NFR‑US‑001** | Usability drop | FR‑TIME‑001, FR‑EXP‑001 | Usability testing, task metric tracking. |

---  

## 10. Security Architecture  

| Layer | Controls | RA‑ID(s) |
|-------|----------|----------|
| **Transport** | TLS 1.2+, mutual TLS for internal services | NFR‑NFT‑001 |
| **Auth** | OAuth2 + JWT, RBAC (FR‑AUTH‑004), MFA via biometric (FR‑AUTH‑001) | FR‑AUTH‑001, FR‑AUTH‑004 |
| **Data at Rest** | RDS encryption (AES‑256), S3 SSE‑S3 | NFR‑NFT‑001, TF‑003 |
| **Input Validation** | Sanitization of all APIs (FR‑AUTH‑001, FR‑EXP‑001) | FR‑AUTH‑001, FR‑EXP‑001 |
| **Audit** | Immutable S3 audit logs, event sourcing (FR‑JOUR‑002) | FR‑JOUR‑002 |
| **Secrets** | AWS Secrets Manager, Vault | NFR‑MA‑001 |
| **Compliance** | Regular penetration test, OWASP ZAP | NFR‑NFT‑001, NFR‑MA‑001 |

---  

## 11. Monitoring & Observability  

| Metric | Service | Alert Threshold | RA‑Ref |
|--------|---------|-----------------|--------|
| **API Latency** | Auth, Payroll | > 300 ms | NFR‑PER‑002 |
| **Session Expiry Rate** | Auth Service | > 5 % per hour | FR‑AUTH‑002 |
| **Push Delivery** | Notifier | < 95 % delivery after 5 min | FR‑NFT‑001 |
| **Error Rate** | All services | > 2 % 5‑min avg | NFR‑MA‑001 |
| **CPU / Memory** | ECS Fargate | > 80 % sustained | NFR‑SCA‑001 |
| **Database Connections** | RDS | > 1000 concurrent | NFR‑AF‑001 |

---  

## 12. Cost Estimation (Monthly, USD)  

| Service | Monthly Cost (USD) | RA‑Ref |
|---------|--------------------|--------|
| **EC2 / ECS** | 3,000 | NFR‑SCA‑001 |
| **RDS PostgreSQL** | 500 | NFR‑AF‑001 |
| **S3 Storage** | 200 | FR‑EXP‑001, FR‑EXP‑004 |
| **Redis** | 150 | FR‑EXP‑002 |
| **CloudWatch** | 100 | NFR‑MA‑001 |
| **GitHub Actions** | 80 | MF‑001 |
| **Datadog** | 250 | NFR‑AVA‑001 |
| **DevOps (IAM, Route 53)** | 120 | TF‑003 |
| **Contingency (10 %)** | 395 | – |
| **Total** | **≈ 4,495** | – |

---  

## 13. Recommendations & Next Steps  

1. **Approve Phase 1** (authentication, login, session timeout) – aligns with FR‑AUTH‑001/002.  
2. **Set up CI/CD pipeline** (Phase 2) to meet MF‑001 and NFR‑MA‑001.  
3. **Schedule biometric SDK integration** (FR‑AUTH‑001) – test on both Android 9+ and iOS 13+.  
4. **Perform load test** (100 k users) before Phase 3 to verify NFR‑SCA‑001.  
5. **Kick‑off security audit** (Owasp ZAP) early in Phase 2.  

---  

## 14. Traceability Matrix  

| FR | NFR | TC | PE | SE | EC |
|----|-----|----|----|----|----|
| FR‑AUTH‑001 | NFR‑NFT‑001 | TC‑001 | PE‑001 | SE‑001 | EC‑002 |
| FR‑AUTH‑002 | NFR‑AF‑001 | TC‑003 | SE‑001 | EC‑001 |
| FR‑PAY‑001 | NFR‑PER‑001 | TC‑004 | PE‑002 | EC‑006 |
| FR‑PAY‑003 | NFR‑PER‑001 | TC‑005 | PE‑003 | EC‑007 |
| FR‑SCH‑001 | NFR‑US‑001 | TC‑006 | SU‑001 | EC‑005 |
| FR‑SCH‑002 | NFR‑AF‑001 | TC‑002 | SU‑002 | EC‑002 |
| FR‑SCH‑003 | NFR‑SCA‑001 | TC‑003 | SU‑003 | EC‑003 |
| FR‑ORG‑001 | NFR‑US‑001 | TC‑007 | SU‑002 | EC‑004 |
| FR‑CHAT‑001 | NFR‑NFT‑001 | TC‑008 | SU‑003 | EC‑001 |
| FR‑EXP‑001 | NFR‑MA‑001 | TC‑009 | SU‑004 | EC‑008 |
| FR‑EXP‑002 | NFR‑AF‑001 | TC‑004 | SE‑002 | EC‑005 |
| FR‑JOUR‑001 | NFR‑MA‑001 | TC‑009 | SU‑004 | EC‑008 |

---  

*End of Design & Estimation (DE) Document – DE.md*