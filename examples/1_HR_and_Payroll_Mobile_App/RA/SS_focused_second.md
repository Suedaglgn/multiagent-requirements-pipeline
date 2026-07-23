# Software Requirements Specification (SRS)

## 1. Introduction  

### Purpose  
The purpose of this document is to provide a comprehensive, development‑ready specification of the **Paylocity Employee & Supervisor Application**. It defines all functional, non‑functional, technical, and contextual requirements that govern the system’s design, implementation, and operation. The specification enables the delivery team to build, test, and maintain a secure, high‑performance, and maintainable solution.

### Scope  
| Item | Description |
|------|-------------|
| **In‑Scope** | All employee‑centric features (paycheck view, clock‑in/out, messages, tasks, schedule, org‑chart, push notifications, etc.) and supervisor‑centric features (time‑off/expense approval, journal entries, schedule editing). |
| **Out‑of‑Scope** | Integration with third‑party payroll processors outside Paylocity, mobile app for external devices, or legacy on‑premise systems. |
| **Exclusions** | Hardware provisioning, physical office equipment, and legacy reporting tools. |
| **Definitions** | See Table 1 below. |
| **Audience** | Software development team, quality assurance, product owners, security analysts, and operations staff. |

### Definitions Table  

| ID | Definition |
|----|------------|
| **FR** | Functional Requirement – a user‑level capability the system must provide. |
| **NFR** | Non‑Functional Requirement – quality attribute not directly tied to a user function. |
| **TC** | Technical Constraint – hard limit imposed by technology or business policy. |
| **EDGE** | Edge Case – exceptional situation that deviates from normal operation. |
| **SMART** | A requirement that is Specific, Measurable, Achievable, Relevant, and Testable. |
| **PAY** | Paylocity – the cloud‑based platform that serves as the data source. |
| **BIO** | Biometric authentication – fingerprint/face‑scan login. |
| **ENC** | Encrypted transmission – data protected in‑flight using TLS 1.3. |
| **SUP** | Supervisor – an employee with higher authority level. |

## 2. Functional Requirements  

### 2.1 Authentication & Security  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-AUTH-001** | Employee biometric login capability | The employee may authenticate using fingerprint or facial recognition via the mobile/web interface. The biometric data is never stored; only a secure token is generated. | High |
| **FR-AUTH-002** | Fallback to PIN/username/password login | If biometric authentication fails or is unavailable, the user may enter a PIN or password derived from Paylocity credentials. | High |
| **FR-AUTH-003** | Session authentication on login | A secure session token is issued immediately after successful authentication. | High |
| **FR-AUTH-004** | Session timeout after inactivity | A session token is invalidated after 15 minutes of no user interaction (mouse/keyboard/voice). | Medium |
| **FR-AUTH-005** | Password expiration policy | Passwords expire every 90 days; a reminder is sent 7 days prior. | Medium |
| **FR-AUTH-006** | Account lockout after repeated failed attempts | After three consecutive authentication failures, the account is locked for 30 minutes. | Medium |
| **FR-AUTH-007** | Secure token revocation on logout | All active tokens are revoked within 5 seconds of logout. | High |

### 2.2 Paycheck & Payroll  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-PAY-001** | Employee view of current paycheck | The employee sees total gross, deductions, and net pay in a single, formatted view. | High |
| **FR-PAY-002** | Employee view of historical pay | The employee can filter and view pay history for the last 12 months. | High |
| **FR-PAY-003** | Early wage request submission | The employee may submit a request for a portion of earned wages before payday; request must be approved by employer. | High |
| **FR-PAY-004** | Wage request approval workflow | Approval occurs within 24 hours; system logs approval/rejection with timestamp. | High |
| **FR-PAY-005** | Denial of early wage request | If employer denies, a clear message explains the reason and provides a reason code. | Low |
| **FR-PAY-006** | Real‑time wage availability notification | The system sends a push notification when earned wages become payable. | High |
| **FR-PAY-007** | Wage request cancellation | The employee can cancel a pending wage request within 5 minutes of submission. | Low |

### 2.3 Clock‑In / Clock‑Out  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-CLOCK-001** | Biometric clock‑in | Employee’s biometric scan logs a clock‑in event with timestamp and device ID. | High |
| **FR-CLOCK-002** | Manual clock‑in via PIN | Employee can input PIN to clock‑in if biometric unavailable. | Medium |
| **FR-CLOCK-003** | Clock‑out must be paired with clock‑in | A clock‑out cannot be created without a matching clock‑in. | High |
| **FR-CLOCK-004** | Clock‑out recorded within 5 minutes of clock‑in | If clock‑in is canceled, a clock‑out after 5 minutes is automatically rejected. | Medium |
| **FR-CLOCK-005** | Clock‑out without clock‑in | The system flags the event for supervisor review. | Low |

### 2.4 Messaging  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-MSG-001** | In‑app chat with colleagues | Employees can send/receive instant messages within the app. | High |
| **FR-MSG-002** | Push notifications for messages | Unread messages trigger a push notification with read count. | High |
| **FR-MSG-003** | Message read receipt | When a message is read, a read‑receipt is logged. | Medium |
| **FR-MSG-004** | Message deletion (self‑service) | The employee can delete their own messages after 24 hours. | Low |
| **FR-MSG-005** | Message export (for HR) | HR can export all messages for a date range for audit. | Low |

### 2.5 Task Management  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-TASK-001** | Create task | Employee can create a task with description, assignee, due date, and priority. | High |
| **FR-TASK-002** | Assign task | Assignee receives notification and can accept/reject. | High |
| **FR-TASK-003** | Task completion confirmation | Completion triggers an automatic status change. | High |
| **FR-TASK-004** | Task history view | Employee can view all tasks and their completion history. | Medium |
| **FR-TASK-005** | Task priority levels | Levels: P1 (critical), P2 (high), P3 (medium), P4 (low). | Low |
| **FR-TASK-006** | Duplicate task detection | System prevents creation of duplicate tasks for same assignee within 24 hours. | Low |

### 2.6 Company Directory  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-DIR-001** | Search employee directory | Search by name, email, or department. | High |
| **FR-DIR-002** | Directory update (self‑service) | Employee can update own profile fields. | High |
| **FR-DIR-003** | Directory synchronization | Changes propagate within 5 minutes across all employee devices. | Medium |
| **FR-DIR-004** | Directory access control | Supervisors can view subordinates only. | Low |
| **FR-DIR-005** | Directory view lockout | If >30 days since last login, directory view is limited. | Low |

### 2.7 Community Hub  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-COMM-001** | View community hub | Hub displays announcements, events, and resources. | High |
| **FR-COMM-002** | Add comment to hub post | Users can reply to posts with up to 500 characters. | High |
| **FR-COMM-003** | Hub notification | New posts trigger push notification. | High |
| **FR-COMM-004** | Hub post deletion (self) | Post can be deleted after 24 hours. | Low |
| **FR-COMM-005** | Hub analytics | Supervisor can view engagement metrics (views, replies). | Low |

### 2.8 Wage Requests (Early Pay)  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-WAGE-001** | Request early wage | Employee submits request for up to 50 % of earned wages before payday. | High |
| **FR-WAGE-002** | Employer approval UI | Supervisor views pending requests with approval/reject buttons. | High |
| **FR-WAGE-003** | Request cancellation | Request can be cancelled up to 30 minutes before approval. | Low |
| **FR-WAGE-004** | Approval deadline | Approval must be completed within 24 hours or request is auto‑rejected. | High |
| **FR-WAGE-005** | Approval logging | All approvals are logged with user ID, timestamp, and outcome. | High |

### 2.9 Schedule & Timesheet  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-SCHED-001** | View personal schedule | Employee sees upcoming shifts and timesheet entries. | High |
| **FR-SCHED-002** | Edit personal schedule | Employee can adjust shift times within policy limits. | High |
| **FR-SCHED-003** | Supervisor schedule edit | Supervisor can create, view, edit, and delete shifts. | High |
| **FR-SCHED-004** | Shift approval workflow | Shift edits require supervisor approval within 24 hours. | High |
| **FR-SCHED-005** | Overtime detection | System flags shifts exceeding 8 hours per day. | Medium |
| **FR-SCHED-006** | Timesheet export | Timesheet data can be exported CSV for HR. | Low |

### 2.10 Interactive Org Chart  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-ORG-001** | View org chart | Employee sees hierarchical structure up to 5 levels deep. | High |
| **FR-ORG-002** | Org chart filter | Filter by department or manager. | Low |
| **FR-ORG-003** | Org chart unavailable | If a department lacks org data, chart shows “Data not available”. | Low |
| **FR-ORG-004** | Org chart sync | Changes (e.g., new manager) sync within 10 minutes. | Low |
| **FR-ORG-005** | Org chart read‑only | Employees cannot edit org chart directly. | Low |

### 2.11 Chat & Notifications  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-CHAT-001** | In‑app chat with colleagues | Real‑time messaging limited to 500 characters. | High |
| **FR-CHAT-002** | Push notifications for chats | New chat messages trigger push alerts. | High |
| **FR-CHAT-003** | Chat read receipt | When a message is read, a read‑receipt is recorded. | Medium |
| **FR-CHAT-004** | Chat archive | Archived messages are retrievable 30 days later. | Low |
| **FR-CHAT-005** | Chat limit | Maximum 100 active chats per user. | Low |

### 2.12 Push Notifications  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-PUSH-001** | Time‑off request approved | Employee receives push when request is approved. | High |
| **FR-PUSH-002** | Wages become available | Employee receives push when early‑wage request is approved. | High |
| **FR-PUSH-003** | Chat message | New chat message triggers push. | High |
| **FR-PUSH-004** | Notification delivery failure | Delivery attempt logged; user receives fallback email. | Medium |
| **FR-PUSH-005** | Notification opt‑out | User can disable push for specific categories. | Low |

### 2.13 Supervisor Tools  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| **FR-SUP-001** | Submit time‑off request | Supervisor can create a time‑off request with justification. | High |
| **FR-SUP-002** | Approve/reject time‑off | Supervisor can approve or reject; approval triggers notification. | High |
| **FR-SUP-003** | Review timecard | Supervisor can view and edit timecard entries. | High |
| **FR-SUP-004** | Approve expense reports | Supervisor can approve or reject expense reports. | High |
| **FR-SUP-005** | Journal entry creation | Supervisor can add journal entries for direct reports. | High |
| **FR-SUP-006** | Journal entry editing | Supervisor can edit existing journal entries. | Medium |
| **FR-SUP-007** | Schedule & shift management | Supervisor can create, edit, and delete shifts. | High |
| **FR-SUP-008** | Schedule sync | Schedule changes sync within 5 minutes to employee view. | Low |

## 3. Non‑Functional Requirements  

| ID | Category | Requirement | Measurable Metric | Target |
|----|----------|-------------|-------------------|--------|
| **NFR-SEC-001** | Security | All data in transit encrypted with TLS 1.3 | % of API calls using TLS 1.3 | 100 % |
| **NFR-SEC-002** | Security | AES‑256 encryption for stored sensitive data | Audit log entry count | 0 breaches |
| **NFR-PERF-001** | Performance | API response time ≤ 200 ms under 95th percentile | 95th‑pct response time | ≤ 200 ms |
| **NFR-AVAIL-001** | Availability | System uptime | Uptime per month | ≥ 99.95 % |
| **NFR-SCAL-001** | Scalability | Support 10,000 concurrent users without degradation | Concurrent load test | 10 k users |
| **NFR-USR-001** | Usability | Task completion time ≤ 5 minutes (average) | % tasks completed within 5 min | 90 % |
| **NFR-MNT-001** | Maintainability | Code coverage ≥ 80 % | Unit test coverage | ≥ 80 % |
| **NFR-ENV-001** | Environment | Application runs on AWS EC2 with auto‑scale | Auto‑scale trigger latency ≤ 2 seconds | ≤ 2 s |

## 4. Technical Constraints & Integration  

| ID | Description |
|----|-------------|
| **TC-INT-001** | The application must integrate with Paylocity’s API v2 only. |
| **TC-INT-002** | All user authentication must be performed via Paylocity’s OAuth token. |
| **TC-INT-003** | The application must respect company‑specific security roles (e.g., “Viewer”, “Editor”). |
| **TC-INT-004** | Biometric hardware must support Android 8+ and iOS 12+. |
| **TC-INT-005** | Data synchronization must occur within 10 minutes for org‑chart changes. |
| **TC-INT-006** | Push notification service must be Paylocity‑hosted, with no third‑party relay. |
| **TC-INT-007** | The system must handle early wage request denial gracefully without data loss. |
| **TC-INT-008** | All logs must be sent to a secure, HIPAA‑compliant SIEM. |

## 5. User Personas & Use Cases  

| Persona | Primary Action | Success Condition |
|---------|----------------|--------------------|
| **Employee** | View paycheck | Paycheck displayed within 2 seconds, no errors. |
| **Employee** | Clock in/out | Clock‑in/out recorded, session authenticated. |
| **Employee** | Request early wage | Approval received ≤ 24 hours, wage credited. |
| **Supervisor** | Approve time‑off | Approval sent to employee within 24 hours. |
| **Supervisor** | Review org chart | Org chart updates within 10 minutes of change. |
| **Employee** | Send chat | Message delivered to recipient within 10 seconds. |

## 6. Edge Cases, Risks, and Gaps  

| ID | Edge Case | Impact | Mitigation |
|----|-----------|--------|------------|
| **EDGE-001** | User lacks Paylocity credentials | System forces login page; cannot proceed. | Show clear error, provide link to recover credentials. |
| **EDGE-002** | Biometric authentication fails | Fallback to PIN; session remains active. | Log failure, trigger account lock after 3 attempts. |
| **EDGE-003** | Session timeout prevents access | Employee cannot use app after 15 min. | Allow “resume” option with reminder. |
| **EDGE-004** | Encryption breach | Data intercepted. | Continuous TLS monitoring; alert SOC on anomalies. |
| **EDGE-005** | Company does not support early wage request | Feature disabled per TC‑INT‑007. | UI shows “Not supported”. |
| **EDGE-006** | Org chart unavailable for certain departments | Employee sees placeholder. | Use fallback message; log missing data. |
| **EDGE-007** | Push notification not delivered | User unaware of event. | Retry mechanism, email fallback. |
| **EDGE-008** | Supervisor lacks rights to view employee’s paycheck | UI hides view. | Role‑based access control validation. |

## 7. Assumptions  

| ID | Assumption |
|----|------------|
| **ASS-001** | Paylocity provides API documentation for all listed functions. |
| **ASS-002** | All employees have smartphones compatible with biometric APIs. |
| **ASS-003** | Supervisors have access to a desktop client for approval tasks. |
| **ASS-004** | The company’s HR policy permits early wage requests up to 50 %. |
| **ASS-005** | Push notification service is available for all supported devices. |
| **ASS-006** | No data privacy regulations (e.g., GDPR) restrict early wage data export. |

## 8. Traceability Matrix  

| FR ID | NFR ID(s) | TC ID(s) |
|-------|-----------|----------|
| FR-AUTH-001 | NFR-SEC-001, NFR-SEC-002 | TC-INT-003 |
| FR-AUTH-002 | NFR-SEC-001 | TC-INT-003 |
| FR-AUTH-003 | NFR-SEC-002 | TC-INT-003 |
| FR-AUTH-004 | NFR-AVAIL-001 | TC-INT-001 |
| FR-PAY-001 | NFR-PERF-001, NFR-AVAIL-001 | TC-INT-001 |
| FR-PAY-003 | NFR-SEC-002 | TC-INT-006 |
| FR-CLOCK-001 | NFR-SEC-001 | TC-INT-003 |
| FR-MSG-001 | NFR-SEC-002, NFR-USR-001 | TC-INT-005 |
| FR-TASK-001 | NFR-PERF-001 | TC-INT-001 |
| FR-DIR-001 | NFR-AVAIL-001 | TC-INT-004 |
| FR-COMM-001 | NFR-SEC-002, NFR-USR-001 | TC-INT-006 |
| FR-WAGE-001 | NFR-AVAIL-001 | TC-INT-007 |
| FR-SCHED-001 | NFR-SEC-002 | TC-INT-002 |
| FR-ORG-001 | NFR-AVAIL-001 | TC-INT-004 |
| FR-CHAT-001 | NFR-SEC-002 | TC-INT-006 |
| FR-PUSH-001 | NFR-SEC-002, NFR-PUSH-005 | TC-INT-006 |
| FR-SUP-001 | NFR-SEC-002 | TC-INT-003 |
| FR-SUP-003 | NFR-SEC-002 | TC-INT-003 |

---  

*This SRS document is the definitive source for all requirements governing the Paylocity Employee & Supervisor Application. It must be maintained in sync with any changes in business rules or technology constraints.*