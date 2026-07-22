# Software Requirements Specification (SRS)

## Paylocity Mobile Application

**Version:** 1.0
**Date:** 2026-04-27
**Status:** Draft

---

## 1. Introduction

### 1.1 Purpose

This document defines the functional and non-functional requirements for the Paylocity Mobile Application, a workforce management platform enabling employees and supervisors to access HR, payroll, scheduling, and social collaboration features from mobile devices. It serves as the authoritative reference for design, development, testing, and acceptance of the application.

### 1.2 Scope

The application provides two primary role-based experiences — Employee and Supervisor — encompassing payroll viewing, time and attendance, scheduling, expense management, social collaboration (Community), earned wage access, push notifications, and organizational directory services. The app operates as a client of Paylocity's backend services; employer must be an active Paylocity client.

### 1.3 Definitions

| Term | Definition |
|------|-----------|
| EWA | Earned Wage Access — ability to request a portion of earned wages before payday |
| Community | Paylocity's social collaboration hub for company-wide communication |
| Org Chart | Interactive visualization of organizational hierarchy |
| Timecard | Digital record of employee clock-in/out events and hours worked |
| Journal Entry | Supervisor note or record associated with a direct report |
| Biometric Auth | Device-level authentication using fingerprint or facial recognition |
| Security Role | Employer-configured permission set controlling feature access per user |

### 1.4 Intended Audience

| Audience | Usage |
|----------|-------|
| Development Team | Implementation of features and integrations |
| QA Engineers | Test case derivation and validation |
| Product Owners | Scope validation and prioritization |
| UX Designers | Interaction and workflow design |
| Security Team | Compliance and threat-model review |

---

## 2. Functional Requirements

### 2.1 Authentication & Session Management

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-AUTH-001 | Credential Login | The system shall authenticate users via Paylocity credentials (username + password) against the Paylocity identity server. | High |
| FR-AUTH-002 | Biometric Login | The system shall support device biometric authentication (fingerprint, face recognition) as a secondary quick-login method after initial credential login. | High |
| FR-AUTH-003 | Session Timeout | The system shall automatically terminate an inactive session after a configurable idle period (default 15 minutes) and redirect to the login screen. | High |
| FR-AUTH-004 | Employer Validation | The system shall verify that the user's employer is an active Paylocity client before granting access. | High |
| FR-AUTH-005 | Role-Based Access | The system shall enforce security role rights configured by the employer, showing or hiding features per the user's assigned role. | High |
| FR-AUTH-006 | Secure Token Storage | The system shall store authentication tokens in the device's secure enclave/keystore, never in plain text. | High |
| FR-AUTH-007 | Logout | The system shall provide an explicit logout action that clears session tokens and cached sensitive data from the device. | Medium |

### 2.2 Home Screen & Navigation

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-HOME-001 | Dashboard Overview | The home screen shall display quick-access cards for paycheck, clock-in/out, messages, and pending tasks. | High |
| FR-HOME-002 | Customizable Navigation | Users shall be able to reorder and pin navigation items to personalize quick access to most-used features. | Medium |
| FR-HOME-003 | Task Summary | The home screen shall show a count badge of outstanding actionable tasks (approvals, messages, requests). | Medium |
| FR-HOME-004 | Navigation Persistence | Customized navigation layout shall persist across sessions for the same user. | Low |

### 2.3 Payroll & Pay Information

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-PAY-001 | Current Pay Stub View | The system shall display the most recent pay stub with gross pay, deductions, taxes, and net pay. | High |
| FR-PAY-002 | Historical Pay Access | The system shall provide a searchable, scrollable list of all historical pay stubs. | High |
| FR-PAY-003 | Pay Stub Detail | Each pay stub shall be expandable to show line-item breakdowns of earnings, deductions, and employer contributions. | Medium |
| FR-PAY-004 | Pay Stub Download | Users shall be able to download pay stubs as PDF documents to the device. | Low |

### 2.4 Earned Wage Access (EWA)

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-EWA-001 | EWA Request | Employees shall be able to request a transfer of a portion of their accrued but unpaid wages. | High |
| FR-EWA-002 | EWA Balance Display | The system shall display the current available EWA balance based on hours worked since last pay period. | High |
| FR-EWA-003 | EWA Limits | The system shall enforce employer-configured maximum withdrawal percentages and daily/per-period caps. | High |
| FR-EWA-004 | EWA Transaction History | The system shall display a log of all past EWA requests with status, amount, and date. | Medium |

### 2.5 Time & Attendance

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-TIME-001 | Clock In | Employees shall clock in via a single tap with timestamp recorded to the second. | High |
| FR-TIME-002 | Clock Out | Employees shall clock out via a single tap with timestamp recorded to the second. | High |
| FR-TIME-003 | Timesheet View | Employees shall view their weekly/biweekly timesheet showing daily hours, breaks, and totals. | High |
| FR-TIME-004 | Schedule View | Employees shall view their upcoming work schedule with shift times, locations, and assigned roles. | High |
| FR-TIME-005 | Time-Off Request | Employees shall submit time-off requests specifying type (vacation, sick, personal), date range, and notes. | High |
| FR-TIME-006 | Time-Off Status | Employees shall view the approval status (pending, approved, denied) of all submitted time-off requests. | Medium |
| FR-TIME-007 | Historical Timesheet | Employees shall access historical timesheets for previous pay periods. | Medium |
| FR-TIME-008 | GPS/Location Stamp | The system shall optionally capture GPS coordinates at clock-in/out if enabled by employer policy. | Low |

### 2.6 Supervisor — Time & Schedule Management

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-SUP-001 | Timecard Approval | Supervisors shall review and approve/reject timecards of direct reports with optional comments. | High |
| FR-SUP-002 | Time-Off Approval | Supervisors shall approve or deny time-off requests with real-time push notification delivery to the requester. | High |
| FR-SUP-003 | Schedule Creation | Supervisors shall create weekly schedules assigning shifts to direct reports. | High |
| FR-SUP-004 | Schedule Edit | Supervisors shall edit and publish schedule changes; affected employees receive push notifications. | High |
| FR-SUP-005 | Shift Swap Management | Supervisors shall view and approve shift swap requests between employees. | Medium |
| FR-SUP-006 | Time-Off Dashboard | Supervisors shall view a calendar overlay of all direct reports' approved time-off for capacity planning. | Medium |

### 2.7 Supervisor — Expense & Journal

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-EXP-001 | Expense Report Review | Supervisors shall review submitted expense reports with line-item details and attached receipts. | High |
| FR-EXP-002 | Expense Approval | Supervisors shall approve or reject expense reports with comments. | High |
| FR-JOUR-001 | Journal Entry Create | Supervisors shall create journal entries (notes, coaching records, performance observations) for direct reports. | High |
| FR-JOUR-002 | Journal Entry Edit | Supervisors shall view and edit existing journal entries for their direct reports. | Medium |

### 2.8 Personal Information & Directory

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-DIR-001 | Edit Personal Info | Employees shall update their personal information (address, phone, emergency contacts) subject to employer-allowed fields. | High |
| FR-DIR-002 | Company Directory Search | Users shall search the company directory by name, department, title, or location. | High |
| FR-DIR-003 | Org Chart View | The system shall render an interactive organizational chart allowing drill-down by department and manager. | Medium |
| FR-DIR-004 | Contact from Org Chart | Users shall initiate a message, call, or email directly from an org chart profile card. | Medium |

### 2.9 Notifications & Messaging

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-NOTIF-001 | Push Notifications | The system shall deliver real-time push notifications for: time-off approvals, paycheck availability, chat messages, task assignments, and schedule changes. | High |
| FR-NOTIF-002 | In-App Message Center | The system shall provide an in-app inbox consolidating all notifications with read/unread status. | High |
| FR-NOTIF-003 | Notification Preferences | Users shall configure which notification categories they receive (with employer-mandated minimums). | Medium |
| FR-NOTIF-004 | Badge Count | The app icon shall display an unread notification badge count on supported OS platforms. | Low |

### 2.10 Community (Social Collaboration)

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR-COMM-001 | Feed View | Users shall view a chronological feed of company posts from leaders and peers. | High |
| FR-COMM-002 | Post Interaction | Users shall like, comment on, and share posts within Community. | Medium |
| FR-COMM-003 | Post Creation | Authorized users shall create text and media posts to designated channels. | Medium |
| FR-COMM-004 | Mentions & Tagging | Users shall mention colleagues using @-tags in posts and comments, triggering a notification to the mentioned user. | Low |

---

## 3. Non-Functional Requirements

### 3.1 Security

| ID | Title | Metric / Criteria | Priority |
|----|-------|--------------------|----------|
| NFR-SEC-001 | Data Encryption in Transit | All API traffic shall use TLS 1.2+ with certificate pinning. | High |
| NFR-SEC-002 | Data Encryption at Rest | Sensitive cached data on-device shall be encrypted using AES-256. | High |
| NFR-SEC-003 | Session Timeout | Sessions shall expire after ≤ 15 minutes of inactivity (configurable by employer, max 30 min). | High |
| NFR-SEC-004 | Penetration Testing | The app shall pass annual third-party penetration testing with zero critical findings. | High |
| NFR-SEC-005 | OWASP Mobile Compliance | The app shall comply with OWASP Mobile Top 10 guidelines. | High |

### 3.2 Performance

| ID | Title | Metric / Criteria | Priority |
|----|-------|--------------------|----------|
| NFR-PERF-001 | API Response Time | 95th percentile API response time shall be ≤ 2 seconds under normal load. | High |
| NFR-PERF-002 | App Launch Time | Cold start to interactive home screen shall be ≤ 3 seconds on devices released within the last 3 years. | High |
| NFR-PERF-003 | Clock Action Latency | Clock-in/out confirmation shall complete within ≤ 1 second. | High |
| NFR-PERF-004 | Offline Queueing | Clock events captured offline shall queue locally and sync within 30 seconds of connectivity restoration. | Medium |

### 3.3 Availability & Scalability

| ID | Title | Metric / Criteria | Priority |
|----|-------|--------------------|----------|
| NFR-AVAIL-001 | Uptime SLA | Backend services shall maintain ≥ 99.9% monthly uptime. | High |
| NFR-SCALE-001 | Concurrent Users | The system shall support ≥ 500,000 concurrent mobile sessions without degradation. | High |
| NFR-SCALE-002 | Push Throughput | The notification subsystem shall deliver ≥ 1 million push messages per minute during peak payroll windows. | Medium |

### 3.4 Usability & Maintainability

| ID | Title | Metric / Criteria | Priority |
|----|-------|--------------------|----------|
| NFR-USE-001 | Accessibility | The app shall meet WCAG 2.1 AA standards for mobile. | High |
| NFR-USE-002 | Platform Support | The app shall support iOS 15+ and Android 12+ (latest two major versions). | High |
| NFR-USE-003 | Localization Ready | The UI framework shall support externalized strings for future multi-language support. | Medium |
| NFR-MAINT-001 | CI/CD Pipeline | Releases shall be deployable via automated CI/CD with < 1-hour rollback capability. | Medium |
| NFR-MAINT-002 | Code Coverage | Unit test coverage shall be ≥ 80% for business logic modules. | Medium |

---

## 4. Technical Constraints & Integration

| ID | Constraint | Detail |
|----|-----------|--------|
| TC-001 | Backend Dependency | App requires connectivity to Paylocity's existing REST/GraphQL backend; no standalone mode except offline clock queueing. |
| TC-002 | Employer Client Prerequisite | User access is gated by employer being an active Paylocity client with mobile access enabled. |
| TC-003 | Role Configuration | Feature visibility is driven by employer-managed security roles in the Paylocity admin console; the app does not manage roles itself. |
| TC-004 | Platform SDKs | Push notifications rely on APNs (iOS) and FCM (Android). |
| TC-005 | Biometric APIs | Biometric login uses platform-native APIs (Face ID / Touch ID on iOS, BiometricPrompt on Android). |
| TC-006 | EWA Provider | Earned Wage Access may integrate with a third-party EWA provider API subject to employer enrollment. |

---

## 5. User Personas & Use Cases

### 5.1 Personas

| Persona | Role | Goals |
|---------|------|-------|
| Alex (Employee) | Hourly warehouse worker | Quick clock-in/out, view schedule, check pay stubs on the go |
| Maria (Employee) | Salaried office worker | Review pay, request time off, browse Community, update personal info |
| Jordan (Supervisor) | Team lead, 12 direct reports | Approve timecards and time-off, manage schedules, write journal entries |
| Sam (Supervisor) | Department manager | Approve expenses, review org chart, monitor team capacity via time-off calendar |

### 5.2 Use Cases

| UC-ID | Actor | Title | Main Flow |
|-------|-------|-------|-----------|
| UC-001 | Alex | Clock In at Shift Start | 1. Open app → biometric login. 2. Tap "Clock In" on home screen. 3. System records timestamp (and GPS if enabled). 4. Confirmation displayed. |
| UC-002 | Maria | Request Time Off | 1. Navigate to Time Off. 2. Select dates and type. 3. Add optional note. 4. Submit. 5. Supervisor receives push notification. 6. Maria receives approval/denial notification. |
| UC-003 | Jordan | Approve Timecards | 1. Receive push notification of pending timecards. 2. Open Timecard Approval queue. 3. Review hours for each report. 4. Approve or reject with comment. 5. Employee notified. |
| UC-004 | Maria | Request Earned Wages | 1. Navigate to EWA. 2. View available balance. 3. Enter desired amount within limits. 4. Confirm request. 5. Funds initiated for transfer. |
| UC-005 | Sam | Create Weekly Schedule | 1. Open Scheduling. 2. Select week. 3. Assign shifts to employees via drag-and-drop or form. 4. Publish schedule. 5. Employees receive push notification of new schedule. |
| UC-006 | Alex | Search Org Chart | 1. Open Directory. 2. Tap Org Chart. 3. Search or browse by department. 4. Tap colleague profile. 5. Initiate message or call. |

---

## 6. Edge Cases, Risks & Gaps

| ID | Category | Description | Mitigation |
|----|----------|-------------|------------|
| RISK-001 | Connectivity | User clocks in while offline; timestamp accuracy depends on device clock. | Queue events locally with device timestamp; flag potential drift > 60 seconds for supervisor review. |
| RISK-002 | Security | Stolen device with biometric enrolled could access sensitive payroll data. | Enforce session timeout, remote wipe capability via MDM, and require re-authentication for sensitive actions (EWA transfers). |
| RISK-003 | Data Sync | Concurrent schedule edits by multiple supervisors may cause conflicts. | Implement optimistic locking with last-write-wins conflict resolution and notify supervisors of overwrites. |
| RISK-004 | EWA Abuse | Employee rapidly requests maximum EWA repeatedly. | Enforce per-period caps and cooldown periods; display remaining balance prominently. |
| RISK-005 | Push Delivery | Push notification delivery is not guaranteed by APNs/FCM. | Provide in-app notification center as fallback; batch-retry failed pushes. |
| GAP-001 | Specification | Exact configurable session timeout range is not defined by the employer admin. | Clarify with product owner; default to 15 min, max 30 min. |
| GAP-002 | Specification | Expense report submission by employees is not mentioned — only supervisor approval. | Confirm whether employee expense submission is in scope or handled via web only. |
| GAP-003 | Specification | Multi-language / localization requirements are not stated. | Architecture supports it (NFR-USE-003); explicit language list TBD. |

---

## 7. Assumptions

| ID | Assumption |
|----|-----------|
| A-001 | The employer's Paylocity backend APIs are available, documented, and versioned. |
| A-002 | Biometric hardware availability is validated at runtime; fallback is credential login. |
| A-003 | Employers configure security roles and feature toggles via an existing admin portal, not through the mobile app. |
| A-004 | The EWA feature availability depends on employer opt-in and third-party provider status. |
| A-005 | Push notification tokens are registered at first login and refreshed on each app launch. |
| A-006 | The app will be distributed via Apple App Store and Google Play Store. |
| A-007 | Users have internet access for all features except offline clock-in/out queueing. |

---

## 8. Traceability Matrix

| Requirement ID | Use Case | Persona | Test Area |
|---------------|----------|---------|-----------|
| FR-AUTH-001, FR-AUTH-002 | UC-001 | Alex, Maria, Jordan, Sam | Authentication |
| FR-AUTH-003, NFR-SEC-003 | All | All | Session Security |
| FR-HOME-001, FR-HOME-002 | All | All | Home Screen / UX |
| FR-PAY-001, FR-PAY-002, FR-PAY-003 | UC-002 | Maria, Alex | Payroll |
| FR-EWA-001, FR-EWA-002, FR-EWA-003 | UC-004 | Maria | Earned Wage Access |
| FR-TIME-001, FR-TIME-002 | UC-001 | Alex | Time & Attendance |
| FR-TIME-004, FR-SUP-003, FR-SUP-004 | UC-005 | Jordan, Sam | Scheduling |
| FR-TIME-005, FR-SUP-002 | UC-002 | Maria, Jordan | Time-Off |
| FR-SUP-001 | UC-003 | Jordan | Timecard Approval |
| FR-EXP-001, FR-EXP-002 | UC-003 | Sam | Expense Management |
| FR-JOUR-001, FR-JOUR-002 | UC-003 | Jordan | Journal Entries |
| FR-DIR-001, FR-DIR-002, FR-DIR-003 | UC-006 | Alex, Maria | Directory / Org Chart |
| FR-NOTIF-001, FR-NOTIF-002 | UC-001–UC-006 | All | Notifications |
| FR-COMM-001, FR-COMM-002 | All | Maria, Alex | Community |
| NFR-PERF-001, NFR-PERF-003 | UC-001 | Alex | Performance |
| NFR-SEC-001, NFR-SEC-002 | All | All | Security |
| NFR-AVAIL-001, NFR-SCALE-001 | All | All | Infrastructure |
| NFR-USE-001, NFR-USE-002 | All | All | Accessibility / Platform |

---

*End of Document*
