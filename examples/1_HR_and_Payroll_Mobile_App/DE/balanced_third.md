# Design & Estimation (DE) Document

## 1. Executive Summary
This document outlines the design and estimation for the Paylocity Employee Portal, a web and mobile application to manage HR, payroll, and collaboration workflows. The system supports two user personas: **Employees** and **Supervisors**, with 31 functional requirements (FRs) and 18 non-functional requirements (NFRs) from the RA. Key design considerations include **biometric authentication** (NFR-SEC-001), **real-time push notifications** (FR-EMP-013–FR-EMP-015), and **role-based access control** (TC-003). The architecture prioritizes **scalability**, **security**, and **usability**, with a phased delivery strategy to ensure alignment with Paylocity’s existing infrastructure.

---

## 2. System Architecture

### Architecture Pattern Justification
- **Microservices Architecture**: Enables independent scaling of features (e.g., notifications, payroll) and aligns with NFR-SCAL-001.
- **Event-Driven Design**: Supports real-time push notifications (FR-EMP-013–FR-EMP-015) via message queues (e.g., Kafka).
- **Layered Architecture**: Separates concerns into frontend, backend, and database layers for maintainability.

### ASCII Diagram
```
+-------------------+       +-------------------+       +-------------------+
|   Mobile/Web UI   |<--->|   API Gateway     |<--->|   Microservices   |
| (React + React   |       | (Node.js)         |       | (User, Payroll,  |
| Native)           |       |                   |       | Notification)     |
+-------------------+       +-------------------+       +-------------------+
                                      |                           |
                                      v                           v
+-------------------+       +-------------------+       +-------------------+
|   Message Queue   |<--->|   Database        |<--->|   External APIs   |
| (Kafka)           |       | (PostgreSQL)      |       | (Paylocity, Auth) |
+-------------------+       +-------------------+       +-------------------+
```

### Component List
| Component | Purpose | RA Requirement References |
|---------|---------|--------------------------|
| **User Service** | Manages authentication, roles, and permissions | TC-002, NFR-SEC-004 |
| **Payroll Service** | Handles pay data access and notifications | FR-EMP-010–FR-EMP-012 |
| **Notification Service** | Sends push notifications (e.g., approvals, checks) | FR-EMP-013–FR-EMP-015 |
| **Directory Service** | Enables company-wide search and org chart | FR-EMP-006–FR-EMP-009, FR-EMP-029 |
| **Timesheet Service** | Clock-in/out, schedule management | FR-EMP-026–FR-EMP-028, FR-SUP-006 |
| **Audit Service** | Logs user actions for compliance | NFR-SEC-005 |

---

## 3. Technology Stack

### Frontend
| Tool/Technology | Version | Justification |
|----------------|---------|---------------|
| **React** | 18.2 | Component-based UI for reusable modules (e.g., forms, tables) |
| **React Native** | 0.72 | Cross-platform mobile support for push notifications (FR-EMP-013–FR-EMP-015) |
| **Redux** | 4.2 | State management for complex workflows (e.g., timesheet editing) |
| **Chart.js** | 4.4 | Interactive org chart visualization (FR-EMP-029) |

### Backend
| Tool/Technology | Version | Justification |
|----------------|---------|---------------|
| **Node.js** | 18.x | Scalable event-driven architecture for real-time features (e.g., notifications) |
| **Express.js** | 4.18 | Lightweight framework for REST APIs |
| **Kafka** | 3.3 | Real-time event streaming for notifications (FR-EMP-013–FR-EMP-015) |
| **Passport.js** | 5.0 | OAuth2 and biometric authentication (NFR-SEC-001) |

### Database
| Table | Version | Justification |
|-------|---------|---------------|
| **Users** | PostgreSQL 14 | Role-based access control (TC-003) |
| **Payroll** | PostgreSQL 14 | Historical pay data (FR-EMP-011) |
| **Notifications** | PostgreSQL 14 | Audit logs for notification delivery (NFR-SEC-005) |
| **Timesheets** | PostgreSQL 14 | Clock-in/out tracking (FR-EMP-026–FR-EMP-028) |

---

## 4. Infrastructure & Deployment

### Cloud Services
| Service | Provider | Use Case | RA Requirement References |
|--------|----------|----------|--------------------------|
| **AWS EC2** | Amazon | Compute instances for microservices | NFR-SCAL-001 |
| **AWS S3** | Amazon | Storage for pay stub PDFs (FR-EMP-012) | FR-EMP-012 |
| **AWS RDS** | Amazon | Managed PostgreSQL database | NFR-SEC-002 |
| **AWS CloudFront** | Amazon | CDN for static assets (e.g., React UI) | NFR-PER-002 |

### CI/CD Pipeline
| Stage | Tool | Description |
|-------|------|-------------|
| **Build** | Jenkins | Automate frontend/backend builds |
| **Test** | Jest + Cypress | Unit/integration tests for core workflows (e.g., clock-in) |
| **Deploy** | AWS CodePipeline | Blue-green deployment for zero-downtime releases |

### Environments
| Environment | Purpose | RA Requirement References |
|------------|---------|--------------------------|
| **Dev** | Development and testing | FR-EMP-001–FR-EMP-031 |
| **Staging** | Pre-production validation | NFR-PER-001, NFR-SEC-002 |
| **Prod** | Live deployment | All NFRs and FRs |

---

## 5. Data Flows for Key Use Cases

### Use Case: Clock In (FR-EMP-026)
1. **User**: Taps "Clock In" in the app.
2. **Frontend**: Validates biometric authentication (NFR-SEC-001).
3. **Backend**: Verifies GPS location (FR-EMP-026).
4. **Database**: Logs timestamp in `Timesheets` table.
5. **Notification Service**: Sends confirmation to user (if enabled).

### Use Case: Approve Timecard (FR-SUP-003)
1. **Supervisor**: Navigates to "Timecards" in the app.
2. **Backend**: Fetches data from `Timesheets` and `Users` tables.
3. **Frontend**: Displays editable timecard.
4. **Database**: Updates `Timesheets` status to "Approved."
5. **Notification Service**: Triggers push notification to employee (FR-EMP-013).

### Use Case: Push Notification Delivery (FR-EMP-013–FR-EMP-015)
1. **Event Trigger**: Time-off request is approved.
2. **Notification Service**: Publishes event to Kafka.
3. **Backend**: Subscribes to Kafka topic and sends push notification.
4. **Mobile/Web UI**: Displays notification to user.

---

## 6. Database Schema Overview

### Core Tables
| Table | Columns | RA Requirement References |
|-------|---------|--------------------------|
| **Users** | id, role, biometric_id, session_token | TC-002, NFR-SEC-004 |
| **Payroll** | user_id, pay_date, gross_pay, net_pay | FR-EMP-010–FR-EMP-012 |
| **Timesheets** | user_id, clock_in_time, clock_out_time, status | FR-EMP-026–FR-EMP-028 |
| **Notifications** | user_id, message, timestamp, read_status | FR-EMP-013–FR-EMP-015 |

---

## 7. API Surface Overview

### Core Endpoints
| Service | Endpoint | Method | Description | RA Requirement References |
|---------|----------|--------|-------------|--------------------------|
| **User** | `/api/users` | GET/POST | Manage user profiles | FR-EMP-001–FR-EMP-005 |
| **Payroll** | `/api/payroll` | GET | Retrieve pay data | FR-EMP-010–FR-EMP-012 |
| **Notification** | `/api/notifications` | GET/POST | Send/receive notifications | FR-EMP-013–FR-EMP-015 |
| **Timesheet** | `/api/timesheets` | GET/PUT | Manage clock-in/out | FR-EMP-026–FR-EMP-028 |

---

## 8. Development Effort Estimation

### Module T-Shirt Sizing
| Module | Size | Estimated Hours | Justification |
|--------|------|-----------------|---------------|
| Authentication & RBAC | XL | 200 | Biometric login (NFR-SEC-001), role-based access (TC-003) |
| Payroll & History | L | 150 | Pay stub retrieval (FR-EMP-010–FR-EMP-012) |
| Notifications | L | 120 | Real-time push (FR-EMP-013–FR-EMP-015) |
| Timesheets & Clock-In | XL | 220 | GPS verification (FR-EMP-026), editability (FR-EMP-025) |
| Org Chart & Directory | M | 80 | Interactive visualization (FR-EMP-029) |

### Phased Delivery
| Phase | Features | Duration | Team Composition |
|-------|----------|----------|------------------|
| **Phase 1 (Weeks 1–8)** | User auth, payroll access, basic notifications | 8 weeks | 2 Frontend, 2 Backend, 1 QA |
| **Phase 2 (Weeks 9–16)** | Timesheets, clock-in, org chart | 8 weeks | 3 Frontend, 2 Backend, 1 DevOps |
| **Phase 3 (Weeks 17–20)** | Supervisor workflows, customization | 4 weeks | 2 Frontend, 1 Backend, 1 Security |

---

## 9. Technical Risks & Mitigation

| Risk | Mitigation Strategy | RA Requirement References |
|------|---------------------|--------------------------|
| Biometric login failure | Fallback to 2FA (NFR-SEC-004) | NFR-SEC-001 |
| Notification delays | Use Kafka for reliable event delivery | FR-EMP-013–FR-EMP-015 |
| Data loss from backups | Geographically redundant S3 backups | NFR-AV-002 |
| Inaccessible UI for disabled users | WCAG 2.1 AA compliance testing | NFR-USE-002 |

---

## 10. Security Architecture

### Key Controls
| Layer | Mechanism | RA Requirement References |
|-------|-----------|--------------------------|
| **Authentication** | FIDO2 biometrics + 2FA | NFR-SEC-001, NFR-SEC-004 |
| **Data Protection** | TLS 1.3 (in transit), AES-256 (at rest) | NFR-SEC-002 |
| **Access Control** | Role-based permissions (TC-003) | TC-003 |
| **Audit** | 180-day logs (NFR-SEC-005) | NFR-SEC-005 |

---

## 11. Monitoring & Observability

### Tools & Metrics
| Tool | Purpose | Metrics Collected |
|------|---------|-------------------|
| **Prometheus** | Real-time metrics | CPU, memory, API latency |
| **Grafana** | Dashboards | Notification delivery time (NFR-PER-001) |
| **Sentry** | Error tracking | Crash reports, failed API calls |
| **CloudWatch** | Logs | Session timeouts (NFR-SEC-003) |

---

## 12. Cost Estimation

### Monthly Breakdown (USD)
| Category | Cost | Details |
|----------|------|---------|
| **Cloud Services** | $3,500 | AWS EC2, S3, RDS |
| **Licensing** | $1,200 | Node.js, Kafka, PostgreSQL |
| **Development** | $18,000 | 6 developers (300 hours/month) |
| **QA & Testing** | $3,000 | Automated testing, security audits |
| **Total** | **$25,700** | |

---

## 13. Recommendations & Next Steps

1. **Prioritize Authentication**: Implement biometric login (NFR-SEC-001) first to ensure secure access.
2. **Prototype Notifications**: Validate Kafka-based delivery for real-time alerts (FR-EMP-013–FR-EMP-015).
3. **Conduct Usability Testing**: Ensure 90% task completion rate for UI (NFR-USE-001).
4. **Plan for Scalability**: Use AWS Auto Scaling for 10,000 concurrent users (NFR-PER-003).

---

## 14. Traceability Matrix

| Requirement ID | Source | Status | Verified By |
|----------------|--------|--------|-------------|
| FR-EMP-001 | Employee Personal Info | In Development | QA Team |
| FR-EMP-006 | Directory Search | In Development | QA Team |
| NFR-SEC-001 | Biometric Authentication | Not Started | Dev Team |
| TC-001 | Paylocity Client Only | In Review | Product Manager |
| UC-ClockIn | Employee Clock-In | In Development | QA Team |