# Design & Estimation Document (DE)

## Paylocity Mobile Application

**Version:** 1.0 | **Date:** 2026-04-27 | **Status:** Draft | **Source RA:** RA_1.md v1.0

---

## 1. Executive Summary

This document translates the approved Requirements Analysis (RA_1.md) for the Paylocity Mobile Application into a concrete technical design, infrastructure plan, and development effort estimate. The app serves two role-based personas — **Employee** and **Supervisor** — across payroll viewing, time & attendance, scheduling, EWA, expense management, community collaboration, and directory services. The architecture targets iOS 15+ and Android 12+ (NFR-USE-002) with a cross-platform framework, backed by Paylocity's existing REST/GraphQL APIs (TC-001). The project is estimated at **~28 weeks** across three delivery phases with a 10-person core team.

---

## 2. System Architecture

### 2.1 Pattern Justification

A **cross-platform mobile client (React Native)** with a **Backend-for-Frontend (BFF) gateway** is selected. React Native satisfies dual-platform support (NFR-USE-002) with a single codebase, while the BFF aggregates calls to Paylocity's micro­services, enforces auth token exchange, and insulates the client from backend schema changes (TC-001, TC-003).

### 2.2 Architecture Diagram

```
┌──────────────────────────────────────────────────────────┐
│                    Mobile Client (React Native)          │
│  ┌─────────┐ ┌──────────┐ ┌────────┐ ┌───────────────┐  │
│  │ Auth Mod │ │ Pay/EWA  │ │Time/Sch│ │ Community/Dir │  │
│  └────┬────┘ └────┬─────┘ └───┬────┘ └──────┬────────┘  │
│       └───────────┴───────────┴──────────────┘           │
│                        │  HTTPS/TLS 1.2+                 │
└────────────────────────┼─────────────────────────────────┘
                         ▼
              ┌─────────────────────┐
              │   API Gateway /     │
              │   BFF (Node.js)     │  ← Rate limiting, cert pinning
              └────────┬────────────┘
         ┌─────────────┼──────────────────┐
         ▼             ▼                  ▼
  ┌────────────┐ ┌───────────┐  ┌──────────────────┐
  │ Paylocity  │ │ EWA 3rd   │  │ Push Service     │
  │ REST/GQL   │ │ Party API │  │ (APNs / FCM)     │
  │ Services   │ │ (TC-006)  │  │ (TC-004)         │
  └────────────┘ └───────────┘  └──────────────────┘
```

### 2.3 Component List

| Component | Responsibility | Key RA Refs |
|-----------|---------------|-------------|
| Auth Module | Credential/biometric login, token lifecycle, secure storage | FR-AUTH-001–007, NFR-SEC-001–002 |
| Home / Navigation | Dashboard cards, customizable nav, task badges | FR-HOME-001–004 |
| Payroll Module | Pay stub list, detail, PDF download | FR-PAY-001–004 |
| EWA Module | Balance display, transfer request, history | FR-EWA-001–004, TC-006 |
| Time & Attendance | Clock in/out, timesheets, offline queue | FR-TIME-001–008, NFR-PERF-003–004 |
| Supervisor Module | Timecard/time-off/expense approval, scheduling, journal | FR-SUP-001–006, FR-EXP-001–002, FR-JOUR-001–002 |
| Directory / Org Chart | Search, interactive org chart, contact actions | FR-DIR-001–004 |
| Community Module | Feed, post CRUD, likes, comments, @mentions | FR-COMM-001–004 |
| Notification Service | Push delivery, in-app inbox, badge, preferences | FR-NOTIF-001–004, TC-004 |
| BFF Gateway | API aggregation, auth relay, rate limiting | TC-001, NFR-PERF-001 |

---

## 3. Technology Stack

### 3.1 Frontend

| Technology | Version | Justification |
|-----------|---------|---------------|
| React Native | 0.76+ | Single codebase for iOS/Android (NFR-USE-002); mature ecosystem |
| TypeScript | 5.x | Type safety, maintainability |
| React Navigation | 7.x | Deep-link support, tab/stack navigators |
| Redux Toolkit + RTK Query | 2.x | State management, API caching, offline support (NFR-PERF-004) |
| react-native-keychain | 9.x | Secure token storage in Keychain/Keystore (FR-AUTH-006) |
| react-native-biometrics | 3.x | Face ID / Touch ID / BiometricPrompt (FR-AUTH-002, TC-005) |
| react-native-push-notification | 8.x | APNs + FCM integration (TC-004) |

### 3.2 Backend (BFF)

| Technology | Version | Justification |
|-----------|---------|---------------|
| Node.js | 22 LTS | Non-blocking I/O for gateway pattern |
| Express + Apollo Server | 4.x / 4.x | REST + GraphQL dual support (TC-001) |
| Helmet / rate-limiter-flexible | latest | Security headers, DDoS protection |
| Redis | 7.x | Session cache, push retry queue (NFR-PERF-001) |

### 3.3 Database (Local & Server-Side Cache)

| Store | Engine | Purpose |
|-------|--------|---------|
| On-Device | SQLite (via WatermelonDB) | Offline clock queue, cached pay stubs (NFR-PERF-004) |
| BFF Cache | Redis 7.x | Token cache, rate-limit counters, notification dedup |
| Primary Data | Paylocity existing RDBMS | All authoritative HR/payroll data (TC-001) — not managed by this project |

---

## 4. Infrastructure & Deployment

| Aspect | Choice | Details |
|--------|--------|---------|
| Cloud Provider | AWS (existing Paylocity infra) | ECS Fargate for BFF, ElastiCache for Redis |
| CDN | CloudFront | Static assets, OTA update bundles |
| CI/CD | GitHub Actions → Fastlane | Automated build, test, sign, deploy; < 1-hr rollback (NFR-MAINT-001) |
| Environments | Dev → QA → Staging → Prod | Feature-flag controlled per env |
| App Distribution | App Store + Google Play (A-006) | TestFlight / Firebase App Distribution for pre-release |
| Secrets Mgmt | AWS Secrets Manager | API keys, certificates |

---

## 5. Data Flows — Key Use Cases

### UC-001: Clock In (FR-TIME-001, NFR-PERF-003)

1. User taps **Clock In** → client captures `timestamp` + optional GPS (FR-TIME-008).
2. If **online**: POST `/v1/time/clock` → BFF → Paylocity Time API → 200 OK → UI confirmation (≤ 1 s).
3. If **offline**: event saved to local SQLite queue → synced within 30 s of reconnection (NFR-PERF-004).

### UC-004: EWA Request (FR-EWA-001–003, TC-006)

1. Client fetches `/v1/ewa/balance` → BFF aggregates from Paylocity payroll + 3rd-party EWA provider.
2. User enters amount → client validates against employer caps (FR-EWA-003).
3. POST `/v1/ewa/transfer` → BFF → EWA provider API → confirmation + updated balance.
4. Transaction logged; visible in EWA history (FR-EWA-004).

### UC-002: Time-Off Request (FR-TIME-005, FR-SUP-002, FR-NOTIF-001)

1. Employee submits request → POST `/v1/timeoff` → Paylocity backend.
2. Push notification dispatched to supervisor (APNs/FCM).
3. Supervisor approves/denies → PUT `/v1/timeoff/{id}` → push notification back to employee.

---

## 6. Database Schema Overview (On-Device)

| Table | Columns (key) | Purpose |
|-------|--------------|---------|
| `offline_clock_events` | id, user_id, type (in/out), timestamp, lat, lon, synced_flag | Offline queue (NFR-PERF-004) |
| `cached_paystubs` | id, pay_date, gross, net, json_detail, fetched_at | Read cache (FR-PAY-001–003) |
| `notification_inbox` | id, category, title, body, read_flag, received_at | In-app inbox (FR-NOTIF-002) |
| `user_nav_prefs` | user_id, ordered_items_json | Navigation persistence (FR-HOME-004) |

---

## 7. API Surface Overview (BFF Endpoints)

| Method | Endpoint | Module | RA Refs |
|--------|----------|--------|---------|
| POST | `/v1/auth/login` | Auth | FR-AUTH-001 |
| POST | `/v1/auth/biometric` | Auth | FR-AUTH-002 |
| DELETE | `/v1/auth/session` | Auth | FR-AUTH-007 |
| GET | `/v1/paystubs` | Payroll | FR-PAY-001–002 |
| GET | `/v1/paystubs/:id/pdf` | Payroll | FR-PAY-004 |
| GET | `/v1/ewa/balance` | EWA | FR-EWA-002 |
| POST | `/v1/ewa/transfer` | EWA | FR-EWA-001 |
| POST | `/v1/time/clock` | Time | FR-TIME-001–002 |
| GET | `/v1/time/sheet` | Time | FR-TIME-003 |
| GET/POST | `/v1/timeoff` | Time | FR-TIME-005–006, FR-SUP-002 |
| GET/PUT | `/v1/supervisor/timecards` | Supervisor | FR-SUP-001 |
| GET/POST/PUT | `/v1/supervisor/schedules` | Supervisor | FR-SUP-003–005 |
| GET/PUT | `/v1/supervisor/expenses` | Supervisor | FR-EXP-001–002 |
| GET/POST/PUT | `/v1/supervisor/journals` | Supervisor | FR-JOUR-001–002 |
| GET | `/v1/directory/search` | Directory | FR-DIR-002 |
| GET | `/v1/directory/orgchart` | Directory | FR-DIR-003 |
| GET/POST | `/v1/community/feed` | Community | FR-COMM-001–003 |
| GET/PUT | `/v1/notifications` | Notifications | FR-NOTIF-001–003 |

---

## 8. Development Effort Estimation

### 8.1 Module T-Shirt Sizing

| Module | Size | Est. Weeks | Rationale |
|--------|------|-----------|-----------|
| Auth & Session | M | 3 | Biometric + secure storage + role gating |
| Home / Nav | S | 2 | Dashboard cards, custom nav |
| Payroll & EWA | L | 4 | 3rd-party EWA integration, PDF gen |
| Time & Attendance | L | 4 | Offline queue, GPS, historical sheets |
| Supervisor Suite | XL | 5 | Approvals, scheduling drag-drop, journal CRUD |
| Directory / Org Chart | M | 3 | Interactive chart rendering |
| Community | M | 3 | Feed, comments, media upload |
| Notifications | M | 2 | Push infra (APNs + FCM), in-app inbox |
| BFF Gateway | M | 2 | Aggregation layer, rate limiting |
| **Total** | | **28 weeks** | |

### 8.2 Phased Delivery

| Phase | Duration | Modules | Milestone |
|-------|----------|---------|-----------|
| Phase 1 — Core | Weeks 1–10 | Auth, Home, Payroll, Time & Attendance, BFF | Internal Alpha |
| Phase 2 — Supervisor & EWA | Weeks 11–20 | Supervisor Suite, EWA, Notifications | Closed Beta |
| Phase 3 — Social & Polish | Weeks 21–28 | Community, Directory/Org Chart, accessibility, perf tuning | GA Release |

### 8.3 Team Composition

| Role | Count | Phase |
|------|-------|-------|
| React Native Engineers | 3 | All |
| Node.js / BFF Engineer | 2 | All |
| QA / Automation Engineer | 2 | All |
| UX Designer | 1 | All |
| DevOps / SRE | 1 | All |
| Tech Lead / Architect | 1 | All |

---

## 9. Technical Risks & Mitigation

| ID | Risk | Likelihood | Impact | Mitigation | RA Ref |
|----|------|-----------|--------|------------|--------|
| TR-01 | 3rd-party EWA API instability | Medium | High | Circuit breaker pattern; fallback error state; SLA in contract | TC-006, RISK-004 |
| TR-02 | Offline clock drift > 60 s | Medium | Medium | Flag drifted events; supervisor review queue | RISK-001, NFR-PERF-004 |
| TR-03 | Push delivery unreliability | Medium | Medium | In-app inbox fallback; batch retry (RISK-005) | FR-NOTIF-001–002 |
| TR-04 | Concurrent schedule edit conflicts | Low | Medium | Optimistic locking + version field; last-write-wins with notification | RISK-003 |
| TR-05 | Cross-platform UI inconsistency | Medium | Low | Platform-specific QA matrix; snapshot testing | NFR-USE-002 |

---

## 10. Security Architecture

| Layer | Control | RA Ref |
|-------|---------|--------|
| Transport | TLS 1.2+ with certificate pinning on all API calls | NFR-SEC-001 |
| Token Storage | iOS Keychain / Android Keystore; no plaintext persistence | FR-AUTH-006, NFR-SEC-002 |
| On-Device Encryption | AES-256 for SQLite cache and cached files | NFR-SEC-002 |
| Session Management | Configurable idle timeout (15–30 min); forced re-auth for EWA | FR-AUTH-003, NFR-SEC-003, RISK-002 |
| Biometric Gating | Platform-native APIs; graceful fallback to credentials | FR-AUTH-002, TC-005 |
| Remote Wipe | MDM integration support for stolen device scenarios | RISK-002 |
| Compliance | Annual pen test (NFR-SEC-004); OWASP Mobile Top 10 (NFR-SEC-005) | NFR-SEC-004–005 |

---

## 11. Monitoring & Observability

| Capability | Tool | Coverage |
|------------|------|----------|
| APM / Crash Reporting | Datadog Mobile SDK + Sentry | Client errors, ANR, crash-free rate |
| API Metrics | Datadog APM on BFF | p95 latency (NFR-PERF-001), error rates |
| Uptime Monitoring | AWS CloudWatch + PagerDuty | 99.9% SLA alerting (NFR-AVAIL-001) |
| Push Delivery Tracking | Custom dashboard (FCM/APNs delivery receipts) | Delivery success % (RISK-005) |
| Log Aggregation | ELK Stack (Elasticsearch + Fluentd) | BFF structured logs, audit trail |
| Synthetic Tests | Datadog Synthetics | Clock-in flow, login flow — 5 min intervals |

---

## 12. Cost Estimation (Monthly, USD)

| Item | Monthly Cost | Notes |
|------|-------------|-------|
| AWS ECS Fargate (BFF — 4 tasks) | $600 | Auto-scaling 2–8 tasks |
| AWS ElastiCache Redis (r6g.large) | $350 | Session + rate-limit cache |
| AWS CloudFront CDN | $200 | OTA bundles + static assets |
| AWS Secrets Manager | $20 | ~50 secrets |
| Datadog (APM + Mobile + Logs) | $1,200 | Pro plan, ~10 hosts equivalent |
| Sentry (Mobile Crashes) | $80 | Team plan |
| PagerDuty | $60 | 5 responders |
| Apple Developer Program | $8 | $99/year prorated |
| Google Play Developer | $2 | $25 one-time prorated |
| **Total (run-rate)** | **~$2,520/mo** | Excludes Paylocity core infra |

---

## 13. Recommendations & Next Steps

1. **Resolve GAP-002** — Confirm with product owner whether employee expense submission is in mobile scope or web-only.
2. **Finalize EWA provider contract** — SLA and API sandbox access needed before Phase 2 development (TC-006).
3. **Define localization language list** (GAP-003, NFR-USE-003) to scope string extraction effort.
4. **Conduct threat model workshop** — Align security team on cert-pinning strategy, MDM integration, and pen-test schedule.
5. **Prototype Org Chart rendering** — Evaluate performance with orgs > 5,000 nodes before committing to Phase 3 timeline.
6. **Establish design system** — Shared Figma/Storybook component library to enforce WCAG 2.1 AA (NFR-USE-001).

---

## 14. Traceability Matrix (RA → DE)

| RA Requirement | DE Section | Design Artifact |
|---------------|------------|-----------------|
| FR-AUTH-001–007 | §2.3, §3.1, §7, §10 | Auth Module, BFF login endpoints, Keychain storage |
| FR-HOME-001–004 | §2.3, §6 | Home component, `user_nav_prefs` table |
| FR-PAY-001–004 | §2.3, §5, §6, §7 | Payroll Module, `cached_paystubs`, `/v1/paystubs` |
| FR-EWA-001–004 | §2.3, §5, §7, §9 | EWA Module, `/v1/ewa/*`, circuit breaker |
| FR-TIME-001–008 | §2.3, §5, §6, §7 | Time Module, `offline_clock_events`, `/v1/time/*` |
| FR-SUP-001–006 | §2.3, §7 | Supervisor Module, `/v1/supervisor/*` |
| FR-EXP-001–002, FR-JOUR-001–002 | §2.3, §7 | Supervisor Module, expenses + journals endpoints |
| FR-DIR-001–004 | §2.3, §7 | Directory Module, `/v1/directory/*` |
| FR-NOTIF-001–004 | §2.3, §7, §11 | Notification service, push tracking dashboard |
| FR-COMM-001–004 | §2.3, §7 | Community Module, `/v1/community/*` |
| NFR-SEC-001–005 | §10 | TLS pinning, AES-256, pen test schedule |
| NFR-PERF-001–004 | §5, §6, §11 | BFF caching, offline queue, APM alerting |
| NFR-AVAIL-001, NFR-SCALE-001–002 | §4, §11, §12 | Fargate auto-scale, CloudWatch, push throughput |
| NFR-USE-001–002, NFR-MAINT-001–002 | §3.1, §4, §8 | React Native, CI/CD pipeline, 80% coverage gate |
| TC-001–006 | §2.2, §3, §4 | BFF gateway, APNs/FCM, EWA provider integration |
| RISK-001–005 | §9 | Risk register with mitigations |
| GAP-001–003 | §13 | Recommendations for resolution |

---

*End of Document*
