# Design & Estimation Document for PictureThis App

---

## 1. Executive Summary

The PictureThis app is designed to provide an intuitive, AI-driven platform for plant identification, disease diagnosis, care management, and expert consultation. The system must meet stringent functional requirements (FRs) and non-functional requirements (NFRs), including 98% accuracy in plant identification (FR-IDENT-001), sub-2-second response times (NFR-PERF-002), and 99.9% uptime for expert chat (NFR-AVAIL-001). The architecture prioritizes scalability, security, and cross-platform compatibility while adhering to technical constraints like GDPR compliance (TC-COMP-001) and real-time processing (TC-REAL-001).

---

## 2. System Architecture

### 2.1 Architecture Pattern Justification
- **Microservices Architecture**: Enables independent scaling of components (e.g., AI engine, expert chat) and fault isolation.
- **Cloud-Native Design**: Ensures scalability (NFR-SCAL-001) and high availability (NFR-AVAIL-001) via AWS/Azure.
- **Edge Computing**: Reduces latency for disease diagnosis (TC-REAL-001) by processing image data closer to the user.

### 2.2 ASCII Diagram
```
+-------------------+       +---------------------+       +---------------------+
|   User Interface  |<----->|     AI Engine       |<----->|  Disease Diagnosis  |
| (Mobile/Web)      |       | (TensorFlow/ML)     |       | (Preprocessing)     |
+-------------------+       +---------------------+       +---------------------+
          |                           |                           |
          v                           v                           v
+---------------------+       +---------------------+       +---------------------+
|    Care Management  |<----->|     Expert Chat     |<----->|   Toxic Alert System|
| (Reminders, Light)  |       | (Firebase/Third-Party)|       | (Toxicity DB)       |
+---------------------+       +---------------------+       +---------------------+
          |                           |                           |
          v                           v                           v
+---------------------+       +---------------------+       +---------------------+
|     Plant Catalog   |<----->|    Database (RDBMS) |<----->|    Recommendations  |
| (Wishlist, Tags)    |       | (PostgreSQL/MongoDB)|       | (ML-Based)          |
+---------------------+       +---------------------+       +---------------------+
```

### 2.3 Component List
| Component | Description | RA Requirement |
|----------|-------------|----------------|
| AI Engine | Image recognition and plant identification | FR-IDENT-001, FR-DISEASE-001 |
| Disease Diagnosis | Symptom analysis and treatment recommendations | FR-DISEASE-001, FR-DISEASE-002 |
| Care Management | Reminder scheduling and light tracking | FR-CARE-002, FR-CARE-003 |
| Expert Chat | Real-time botanist consultations | FR-EXPERT-001, FR-EXPERT-002 |
| Toxic Alert | Toxicity detection and warnings | FR-TOXIC-001, FR-TOXIC-002 |
| Plant Catalog | User plant database and wishlist | FR-MANAGE-001, FR-MANAGE-002 |
| Database | Storage for user data, plant profiles, and care logs | NFR-SECURE-001, NFR-SCAL-001 |

---

## 3. Technology Stack

### 3.1 Frontend
| Tool/Language | Version | Justification |
|---------------|---------|---------------|
| React Native | 0.70+ | Cross-platform compatibility (iOS/Android) (NFR-COMPAT-001) |
| Figma | 2023.1 | UI/UX design for accessibility (NFR-ACCESS-001) |
| Webpack | 5.70 | Module bundling for web app (NFR-COMPAT-001) |

### 3.2 Backend
| Tool/Language | Version | Justification |
|---------------|---------|---------------|
| Node.js | 18.x | Scalable server for real-time chat (FR-EXPERT-001) |
| Python (Flask/Django) | 3.9 | Data processing for AI models (FR-IDENT-001) |
| AWS Lambda | 1.0 | Serverless functions for image preprocessing (TC-REAL-001) |

### 3.3 Database
| Database | Version | Justification |
|----------|---------|---------------|
| PostgreSQL | 14.x | Structured storage for user data (NFR-SECURE-001) |
| MongoDB | 6.0 | Unstructured data for plant profiles and care logs (NFR-SCAL-001) |

### 3.4 AI/ML
| Tool/Language | Version | Justification |
|---------------|---------|---------------|
| TensorFlow | 2.12 | Pre-trained models for plant identification (TC-AI-001) |
| OpenCV | 4.5 | Image preprocessing for disease diagnosis (FR-DISEASE-003) |

---

## 4. Infrastructure & Deployment

### 4.1 Cloud Services
| Service | Provider | Justification |
|---------|----------|---------------|
| EC2 | AWS | Scalable compute for AI model processing (NFR-SCAL-001) |
| S3 | AWS | Storage for user-uploaded images (TC-COMP-001) |
| RDS | AWS | Managed PostgreSQL database (NFR-SECURE-001) |
| Lambda | AWS | Serverless functions for real-time tasks (TC-REAL-001) |

### 4.2 CI/CD Pipeline
| Tool | Version | Justification |
|------|---------|---------------|
| GitHub Actions | 2.280 | Automated testing and deployment (NFR-MNTN-001) |
| Jenkins | 2.361 | Continuous integration for backend services (NFR-MNTN-002) |

### 4.3 Environments
| Environment | Purpose | RA Requirement |
|-------------|---------|----------------|
| Dev | Development and testing | NFR-PERF-001 |
| Staging | Pre-production validation | NFR-AVAIL-001 |
| Prod | Live deployment | NFR-SCAL-001 |

---

## 5. Data Flows for Key Use Cases

### 5.1 Identify Plant
1. **User** uploads photo via mobile app (FR-IDENT-002).
2. **AI Engine** processes image (FR-IDENT-001).
3. **Database** retrieves plant data (NFR-SCAL-001).
4. **UI** displays results (NFR-USABLE-001).

### 5.2 Diagnose Disease
1. **User** uploads sick plant photo (FR-DISEASE-001).
2. **Disease Diagnosis Module** analyzes image (FR-DISEASE-003).
3. **AI Engine** suggests treatment (FR-DISEASE-002).
4. **UI** displays recommendations (NFR-PERF-002).

### 5.3 Set Care Reminder
1. **User** selects plant and sets reminder (FR-CARE-002).
2. **Care Management Module** schedules task (FR-CARE-004).
3. **Database** stores reminder data (NFR-SECURE-001).
4. **Push Notification Service** sends alert (NFR-PERF-003).

---

## 6. Database Schema Overview

### 6.1 Tables
| Table | Fields | RA Requirement |
|-------|--------|----------------|
| Users | id, name, email, password_hash, language | NFR-SECURE-001 |
| Plants | id, name, species, toxicity_status, care_guide | FR-IDENT-003, FR-TOXIC-001 |
| CareReminders | id, user_id, plant_id, schedule, status | FR-CARE-002 |
| ExpertChats | id, user_id, expert_id, message, timestamp | FR-EXPERT-001 |
| ToxicAlerts | id, plant_id, warning, severity_level | FR-TOXIC-002 |
| PlantCatalog | id, user_id, plant_id, tags, wishlist | FR-MANAGE-001 |
| Recommendations | id, user_id, plant_id, reason, cost | FR-RECOMMEND-001 |

### 6.2 Indexes
- **Primary Keys**: Auto-incrementing `id` for all tables.
- **Foreign Keys**: `user_id` in CareReminders, ExpertChats, etc.
- **Composite Indexes**: `user_id + plant_id` for PlantCatalog.

---

## 7. API Surface Overview

### 7.1 Endpoints
| Endpoint | Method | Description | RA Requirement |
|----------|--------|-------------|----------------|
| /identify | POST | Submit plant photo for identification | FR-IDENT-001 |
| /diagnose | POST | Upload sick plant photo for disease analysis | FR-DISEASE-001 |
| /reminders | GET/POST | Retrieve/set care reminders | FR-CARE-002 |
| /chat | POST | Send expert consultation message | FR-EXPERT-001 |
| /toxic | GET | Check plant toxicity status | FR-TOXIC-001 |
| /recommendations | GET | Fetch plant purchase suggestions | FR-RECOMMEND-001 |

### 7.2 Response Format
- **JSON** for all responses.
- **Status Codes**: 200 (OK), 400 (Invalid Input), 500 (Server Error).

---

## 8. Development Effort Estimation

### 8.1 Module T-Shirt Sizing
| Module | Size | Effort (Person-Days) | RA Requirement |
|--------|------|----------------------|----------------|
| AI Engine | XL | 120 | FR-IDENT-001, FR-DISEASE-001 |
| Backend | L | 90 | FR-CARE-001, FR-EXPERT-001 |
| Frontend | L | 80 | NFR-USABLE-001 |
| Database | M | 40 | NFR-SCAL-001 |
| Expert Chat | M | 50 | FR-EXPERT-001 |

### 8.2 Phased Delivery
- **Phase 1**: Core Features (Plant ID, Care Reminders) – 6 months.
- **Phase 2**: Disease Diagnosis & Expert Chat – 4 months.
- **Phase 3**: Toxic Alerts & Recommendations – 3 months.

### 8.3 Team Composition
- **AI Engineers**: 2 (Model training, optimization).
- **Frontend Devs**: 3 (Mobile/Web UI).
- **Backend Devs**: 3 (APIs, Database).
- **QA Engineers**: 2 (Testing, security).
- **DevOps**: 1 (CI/CD, Deployment).

---

## 9. Technical Risks & Mitigation

| Risk | Mitigation | RA Requirement |
|------|------------|----------------|
| AI Model Inaccuracy | Retrain models monthly with diverse datasets (RISK-ML-001) | FR-IDENT-001 |
| Data Privacy Breach | Encrypt data in transit (TLS 1.3) and at rest (AES-256) (NFR-SECURE-001) | TC-COMP-001 |
| Expert Chat Downtime | Implement redundant server clusters (RISK-CHAT-001) | NFR-AVAIL-001 |

---

## 10. Security Architecture

### 10.1 Data Protection
- **Encryption**: AES-256 for stored data, TLS 1.3 for transit.
- **Authentication**: OAuth 2.0 for user login, biometric fallback (NFR-SECURE-002).

### 10.2 Access Control
- **RBAC**: Role-based access for experts and admins.
- **Audit Logs**: Track user activity for compliance (NFR-SECURE-003).

### 10.3 Compliance
- **GDPR/CCPA**: Data minimization, user consent, and deletion rights (TC-COMP-001).

---

## 11. Monitoring & Observability

### 11.1 Tools
| Tool | Purpose | RA Requirement |
|------|---------|----------------|
| Prometheus | Metric collection (response time, errors) | NFR-PERF-001 |
| Grafana | Dashboards for system health | NFR-PERF-002 |
| ELK Stack | Log aggregation and analysis | NFR-MNTN-001 |

### 11.2 Metrics
- **Response Time**: <2s for plant ID, <5s for expert chat.
- **Error Rate**: <0.1% for critical features.
- **Uptime**: 99.9% for expert services (NFR-AVAIL-001).

---

## 12. Cost Estimation

### 12.1 Monthly Breakdown (USD)
| Category | Cost | Justification |
|----------|------|---------------|
| Cloud Infrastructure | $15,000 | AWS/Azure for 10M users (NFR-SCAL-001) |
| Development | $20,000 | 10 developers for 12 months (8.5k/hour) |
| AI Model Training | $3,000 | Monthly retraining and updates (TC-AI-001) |
| Third-Party Services | $2,500 | Firebase for chat, OpenCV for image processing |
| QA & Testing | $1,500 | Automated and manual testing for NFR-USABLE-001 |

---

## 13. Recommendations & Next Steps

- **Iterative Development**: Prioritize core features (plant ID, care reminders) in Phase 1.
- **User Testing**: Conduct usability studies for accessibility (NFR-ACCESS-001).
- **Continuous Monitoring**: Implement alerts for system outages (NFR-AVAIL-001).

---

## 14. Traceability Matrix

| Requirement ID | Module | Design Element | Test Case |
|----------------|--------|----------------|-----------|
| FR-IDENT-001 | AI Engine | TensorFlow model | TC-IDENT-001 |
| FR-DISEASE-001 | Disease Diagnosis | OpenCV preprocessing | TC-DISEASE-001 |
| NFR-PERF-001 | Backend | Node.js performance tuning | TC-PERF-001 |
| TC-AI-001 | AI Engine | Model retraining pipeline | TC-AI-001 |