# Design & Estimation Document (DE.md)

## 1. Executive Summary

This document outlines the design and estimation for a plant identification and care application, aligning with the functional and non-functional requirements defined in the Requirements Analysis (RA) document. The system will provide accurate plant species identification, disease diagnosis, care recommendations, and expert consultation, with a focus on scalability, security, and user experience.

Key features include:
- **Plant Identification**: 98% accuracy for 17,000+ species (FR-IDENT-001).
- **Disease Diagnosis**: Symptom detection and treatment recommendations (FR-DIAG-001).
- **Care Tips**: Customized, step-by-step guidance (FR-CARE-001).
- **Expert Consultation**: Secure, real-time chat with horticulturists (FR-EXPERT-001).
- **Performance**: Sub-3-second identification latency (NFR-PERF-002).

The design emphasizes modularity, cloud scalability, and AI-driven processing to meet the RA's technical constraints and non-functional requirements.

---

## 2. System Architecture

### Architecture Pattern Justification
A **microservices architecture** is chosen to ensure scalability, fault isolation, and independent deployment of components. Key services include:
- **AI/ML Service**: For image recognition and disease diagnosis.
- **Core Services**: For user management, reminders, and care tips.
- **Expert Consultation Service**: For real-time and asynchronous messaging.
- **Database Layer**: For structured and unstructured data storage.

### ASCII Diagram
```
+---------------------+
|   User Device       |
| (Mobile/Web)        |
+----------+----------+
           |
           | Upload Photo
           v
+---------------------+
|   AI/ML Service     |
| (Image Recognition) |
+----------+----------+
           |
           | Results
           v
+---------------------+
|   Core Services     |
| (Identification,   |
|  Care Tips, Reminders)|
+----------+----------+
           |
           | Data
           v
+---------------------+
|   Expert Service    |
| (Chat, Validation)  |
+----------+----------+
           |
           | Data
           v
+---------------------+
|   Database Layer    |
| (PostgreSQL, S3)    |
+---------------------+
```

### Component List
| Component | Purpose | Technology | Justification |
|----------|---------|------------|---------------|
| AI/ML Service | Image analysis and disease diagnosis | TensorFlow/PyTorch | Required for FR-IDENT-001 and FR-DIAG-001. |
| Core Services | User management, reminders, care plans | Node.js/Express | Enables modular, scalable backend (NFR-SCAL-001). |
| Expert Service | Chat, validation, expert credentials | WebSocket + Firebase | Supports real-time communication (FR-EXPERT-001). |
| Database Layer | Structured data (users, plants) + unstructured (images) | PostgreSQL + AWS S3 | Balances relational and scalable storage (TC-INT-003). |

---

## 3. Technology Stack

### Frontend
| Tool | Version | Justification |
|------|---------|---------------|
| React Native | 0.70+ | Cross-platform mobile support (TC-INT-002). |
| Web UI | React 18 | Responsive design for desktop users. |
| UI Library | Material-UI | Consistent, accessible components. |

### Backend
| Tool | Version | Justification |
|------|---------|---------------|
| Node.js | 18.x | High-performance, event-driven architecture. |
| Python (AI/ML) | 3.9 | Compatibility with TensorFlow/PyTorch. |
| Express.js | 4.18 | Lightweight REST API framework. |

### Database
| Table | Version | Justification |
|-------|---------|---------------|
| PostgreSQL | 14 | Structured data (users, plants, care tips). |
| MongoDB | 6.0 | Unstructured data (user-submitted photos, chat logs). |
| AWS S3 | N/A | Scalable storage for images and videos (TC-INT-003). |

---

## 4. Infrastructure & Deployment

### Cloud Services
- **AWS**: EC2 (backend), S3 (storage), RDS (PostgreSQL), Lambda (AI processing).
- **Firebase**: Real-time chat and authentication (FR-EXPERT-001).

### CI/CD Pipeline
- **GitHub Actions**: Automated testing and deployment.
- **Docker**: Containerization for consistent environments.
- **Kubernetes**: Orchestration for microservices scalability.

### Environments
| Environment | Purpose | Configuration |
|-------------|---------|---------------|
| Dev | Development | Single-node EC2 instance. |
| Staging | Testing | Load-balanced AWS setup. |
| Production | Live | Auto-scaled, multi-region deployment. |

---

## 5. Data Flows for Key Use Cases

### Use Case: Plant Identification
1. **User Uploads Photo** → Frontend sends to AI/ML Service.
2. **AI Analyzes Image** → Returns species ID (FR-IDENT-001).
3. **Core Service Fetches Data** → Queries PostgreSQL for species details.
4. **Result Sent to User** → Displayed in UI (FR-IDENT-005).

### Use Case: Disease Diagnosis
1. **Photo Uploaded** → AI Service detects symptoms (FR-DIAG-001).
2. **Disease Classified** → Returns category (FR-DIAG-002).
3. **Treatment Plan Generated** → Stored in MongoDB (FR-DIAG-003).
4. **User Receives Recommendations** → Via app notification.

### Use Case: Expert Consultation
1. **User Submits Query** → Stored in Firebase.
2. **Expert Assigned** → WebSocket connection established (FR-EXPERT-001).
3. **Chat Logs Encrypted** → AES-256 (NFR-SEC-001).
4. **Response Sent** → Delivered to user via app.

---

## 6. Database Schema Overview

### Tables
| Table | Fields | Description |
|-------|--------|-------------|
| `users` | id, name, email, role, created_at | User authentication and roles (NFR-SEC-002). |
| `plants` | id, species, family, care_instructions, image_url | Plant species data (FR-IDENT-004). |
| `diagnoses` | id, user_id, plant_id, disease, severity, timestamp | Disease diagnosis history (FR-DIAG-005). |
| `care_tips` | id, plant_id, step, media_url | Step-by-step care instructions (FR-CARE-003). |
| `reminders` | id, user_id, task, frequency, next_scheduled | Reminder scheduling (FR-REMIND-001). |
| `experts` | id, name, credentials, rating, availability | Expert verification (FR-EXPERT-003). |

---

## 7. API Surface Overview

| Endpoint | Method | Description | Associated FR |
|----------|--------|-------------|---------------|
| `/identify-plant` | POST | Upload photo for species identification | FR-IDENT-001 |
| `/diagnose-disease` | POST | Analyze photo for disease symptoms | FR-DIAG-001 |
| `/care-tips` | GET | Retrieve species-specific care instructions | FR-CARE-001 |
| `/set-reminder` | POST | Schedule plant care tasks | FR-REMIND-001 |
| `/chat` | WebSocket | Real-time expert consultation | FR-EXPERT-001 |

---

## 8. Development Effort Estimation

### Module T-Shirt Sizing
| Module | Size | Effort (Person-Days) | Justification |
|--------|------|----------------------|---------------|
| AI/ML Service | Large | 120 | Complex image processing and model training (FR-IDENT-001). |
| Core Services | Large | 90 | User management, reminders, and care plans (FR-CARE-001). |
| Expert Service | Medium | 60 | Real-time chat and validation (FR-EXPERT-001). |
| Frontend | Medium | 45 | Cross-platform UI development (TC-INT-002). |
| Database | Small | 20 | Schema design and integration (TC-INT-003). |

### Phased Delivery
1. **Phase 1 (3 months)**: Core features (identification, care tips, reminders).
2. **Phase 2 (2 months)**: Expert consultation and AI model optimization.
3. **Phase 3 (1 month)**: Scalability, security, and performance tuning.

### Team Composition
- **Frontend Developers**: 2 (React Native/Web).
- **Backend Developers**: 3 (Node.js/Python).
- **AI/ML Engineers**: 2 (TensorFlow/PyTorch).
- **DevOps Engineer**: 1 (CI/CD, infrastructure).
- **QA Engineers**: 2 (Testing, security).

---

## 9. Technical Risks & Mitigation

| Risk | Mitigation | RA Reference |
|------|------------|--------------|
| AI Model Accuracy | Regular retraining with user-submitted data (FR-IDENT-001). | FR-IDENT-001 |
| Expert Chat Overload | Auto-scaling and queue management (NFR-AVAIL-001). | NFR-AVAIL-001 |
| Data Breach | AES-256 encryption and RBAC (NFR-SEC-001). | NFR-SEC-001 |
| Offline Mode Limitations | Prioritize core features (FR-IDENT-003). | FR-IDENT-003 |

---

## 10. Security Architecture

### Key Measures
- **Data Encryption**: AES-256 for data at rest (PostgreSQL) and TLS 1.3 for in-transit (NFR-SEC-001).
- **Access Control**: Role-based authentication (RBAC) with JWT tokens (NFR-SEC-002).
- **Audit Logs**: Store user activity and system access in a secure, immutable format (NFR-SEC-003).

### Compliance
- GDPR for user data privacy.
- HIPAA for expert consultation data (if applicable).

---

## 11. Monitoring & Observability

| Tool | Purpose | Metrics |
|------|---------|---------|
| Prometheus | Performance metrics (response time, error rates) | NFR-PERF-002 |
| ELK Stack | Log aggregation and analysis | NFR-SEC-003 |
| New Relic | Real-time application performance | NFR-AVAIL-001 |
| Grafana | Dashboards for system health | NFR-SCAL-002 |

---

## 12. Cost Estimation

| Category | Monthly Cost (USD) | Justification |
|----------|--------------------|---------------|
| Cloud Infrastructure (AWS) | $2,500 | EC2, S3, RDS, Lambda. |
| Developer Salaries | $15,000 | 8 developers at $1,875/month. |
| AI Model Training | $1,200 | GPU hours for TensorFlow/PyTorch. |
| Third-Party Tools | $300 | Firebase, New Relic, etc. |
| Contingency | $1,000 | Unforeseen expenses. |
| **Total** | **$20,000** | |

---

## 13. Recommendations & Next Steps

1. **Prioritize MVP**: Focus on FR-IDENT-001, FR-DIAG-001, and FR-CARE-001 for Phase 1.
2. **AI Model Training**: Partner with horticulturists to curate training data.
3. **Security Audit**: Conduct penetration testing before launch.
4. **User Testing**: Validate usability with target personas (Parent/Guardian, Pet Owner).

---

## 14. Traceability Matrix

| Requirement ID | Source | Type | Status | DE Section |
|----------------|--------|------|--------|------------|
| FR-IDENT-001 | RA | Functional | In Development | 2, 3, 5 |
| FR-DIAG-001 | RA | Functional | In Development | 2, 5, 7 |
| NFR-SEC-001 | RA | Non-Functional | Not Started | 10 |
| TC-INT-001 | RA | Technical | In Development | 3, 7 |
| FR-EXPERT-001 | RA | Functional | In Development | 2, 7, 10 |