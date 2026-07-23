# Software Requirements Specification — PictureThis Plant Identification App

## 1. Introduction

### 1.1 Purpose
This document defines the software requirements for **PictureThis**, a mobile application that enables users to identify plants, diagnose plant diseases, receive care guidance, consult botanical experts, and manage a personal plant collection — all through photo-based AI recognition. It serves as the authoritative reference for design, development, testing, and stakeholder alignment.

### 1.2 Scope
PictureThis is a cross-platform mobile application (iOS & Android) backed by cloud services. The system encompasses image-based plant identification, disease diagnosis, care scheduling, expert chat, toxicity warnings, plant collection management, and personalized plant recommendations. Monetization, third-party integrations, and administrative back-office tooling are out of scope for this initial release unless explicitly stated.

### 1.3 Definitions

| Term | Definition |
|---|---|
| Plant Identification Engine (PIE) | The AI/ML model service that classifies plant species from images. |
| Disease Diagnosis Engine (DDE) | The AI/ML model service that detects plant diseases from images. |
| Light Meter | A device-sensor-based feature that measures ambient light levels. |
| Gallery Upload | Selecting an existing photo from the device's media library. |
| Expert | A verified botanist or horticulturist available for live consultation. |
| Wishlist | A user-curated list of desired but not yet owned plants. |
| Toxic Plant | A plant species classified as harmful to humans or animals upon ingestion or contact. |

### 1.4 Intended Audience
- **Development Team** — for implementation and technical planning.
- **QA Engineers** — for deriving test cases from each requirement.
- **Product Owners / Stakeholders** — for scope validation and prioritization.
- **UX/UI Designers** — for understanding feature behaviors and user flows.

---

## 2. Functional Requirements

### 2.1 User Account & Authentication

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-AUTH-001 | Email Registration | The system shall allow users to register with email and password, enforcing minimum 8-character passwords with at least one uppercase letter and one digit. | High |
| FR-AUTH-002 | Social Login | The system shall support OAuth 2.0 sign-in via Google, Apple, and Facebook. | High |
| FR-AUTH-003 | Guest Mode | The system shall allow limited functionality (up to 3 identifications per day) without account creation. | Medium |
| FR-AUTH-004 | Password Reset | The system shall send a time-limited (15-min expiry) password reset link to the registered email. | High |
| FR-AUTH-005 | Session Management | The system shall maintain user sessions with JWT tokens, auto-refreshing before expiry and forcing re-login after 30 days of inactivity. | High |
| FR-AUTH-006 | Profile Management | The system shall allow users to update display name, avatar, gardening experience level, and location. | Medium |
| FR-AUTH-007 | Account Deletion | The system shall allow users to permanently delete their account and all associated data within 72 hours of request. | High |

### 2.2 Plant Identification

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-IDEN-001 | Camera Capture | The system shall allow the user to capture a live photo using the device camera for identification. | High |
| FR-IDEN-002 | Gallery Upload | The system shall allow the user to select one or more images from the device gallery for identification. | High |
| FR-IDEN-003 | Species Classification | The PIE shall classify at least 17,000 plant species with a minimum accuracy of 98% on the benchmark dataset. | High |
| FR-IDEN-004 | Top-N Results | The system shall return the top 3 most probable species matches with confidence scores. | High |
| FR-IDEN-005 | Result Detail View | For each identified species, the system shall display common name, scientific name, family, origin, habitat, and a description. | High |
| FR-IDEN-006 | Identification History | The system shall persist every identification result with timestamp, location (if permitted), and source image. | Medium |
| FR-IDEN-007 | Offline Queuing | If the device is offline, the system shall queue the identification request and automatically submit it when connectivity is restored. | Medium |
| FR-IDEN-008 | Multi-Plant Detection | The system shall detect and separately identify multiple plants visible in a single image. | Low |
| FR-IDEN-009 | Feedback Loop | The system shall allow users to confirm or correct identification results; corrections shall feed into model retraining pipelines. | Medium |
| FR-IDEN-010 | Continuous Model Updates | The PIE shall be updated at least quarterly to incorporate new species and improve accuracy. | Medium |

### 2.3 Plant Disease Diagnosis

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-DIAG-001 | Disease Photo Capture | The system shall allow the user to photograph a symptomatic plant part (leaf, stem, root) for diagnosis. | High |
| FR-DIAG-002 | Gallery Disease Upload | The system shall accept gallery images for disease diagnosis. | High |
| FR-DIAG-003 | Disease Classification | The DDE shall identify at least 500 common plant diseases and nutrient deficiencies. | High |
| FR-DIAG-004 | Severity Rating | The system shall provide a severity rating (Mild, Moderate, Severe) for each diagnosed condition. | Medium |
| FR-DIAG-005 | Treatment Recommendations | For each diagnosed disease, the system shall display cause, symptoms summary, recommended treatment steps, and preventive measures. | High |
| FR-DIAG-006 | Product Suggestions | The system shall suggest relevant treatment products (e.g., fungicides, fertilizers) with purchase links where applicable. | Low |
| FR-DIAG-007 | Diagnosis History | The system shall store all diagnosis results linked to the user's plant collection entry, enabling progress tracking over time. | Medium |

### 2.4 Plant Care Tips & Reminders

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-CARE-001 | Species-Specific Care Guide | The system shall provide detailed care instructions (watering frequency, soil type, sunlight, temperature range, humidity) per identified species. | High |
| FR-CARE-002 | Watering Reminder | The system shall allow users to create recurring watering reminders with customizable frequency and time-of-day. | High |
| FR-CARE-003 | Fertilizing Reminder | The system shall support fertilizing schedule reminders linked to species-specific guidance. | High |
| FR-CARE-004 | Misting Reminder | The system shall support misting reminders for humidity-sensitive species. | Medium |
| FR-CARE-005 | Cleaning Reminder | The system shall remind users to clean plant leaves on a configurable schedule. | Medium |
| FR-CARE-006 | Repotting Reminder | The system shall remind users when repotting is recommended based on plant age and growth rate. | Medium |
| FR-CARE-007 | Push Notifications | All reminders shall be delivered as local push notifications, respecting user-defined quiet hours. | High |
| FR-CARE-008 | Light Meter | The system shall use the device's ambient light sensor to measure and display current light intensity (lux) and classify it (low, medium, bright, direct sun). | Medium |
| FR-CARE-009 | Light Tracking History | The system shall log light meter readings over time and display a daily/weekly light exposure chart for each plant location. | Low |
| FR-CARE-010 | Seasonal Care Adjustments | The system shall adjust care recommendations based on the user's hemisphere and current season. | Medium |

### 2.5 Expert Consultation

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-EXPT-001 | Chat Initiation | The system shall allow authenticated users to initiate a one-on-one text chat with an available expert. | High |
| FR-EXPT-002 | Photo Sharing in Chat | Users shall be able to share photos within the expert chat for contextual advice. | High |
| FR-EXPT-003 | 24/7 Availability | The system shall route chat requests to available experts around the clock with a maximum initial response time of 5 minutes. | High |
| FR-EXPT-004 | Expert Profiles | The system shall display expert credentials, specialization areas, and user ratings before chat begins. | Medium |
| FR-EXPT-005 | Chat History | The system shall persist all expert chat transcripts, accessible from the user's profile for 12 months. | Medium |
| FR-EXPT-006 | Rating & Feedback | After each consultation, the system shall prompt the user to rate the expert (1–5 stars) and leave optional text feedback. | Medium |
| FR-EXPT-007 | Escalation Path | If an expert cannot resolve a query, the system shall allow escalation to a senior specialist within the same session. | Low |

### 2.6 Toxic Plant Warning

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-TOXI-001 | Toxicity Flag on Identification | Whenever an identified plant is classified as toxic, the system shall display a prominent warning badge on the result screen. | High |
| FR-TOXI-002 | Toxicity Detail | The warning shall specify toxicity level (mild irritant, moderate, highly toxic), affected groups (pets, children, adults), and toxic plant parts. | High |
| FR-TOXI-003 | Emergency Contact Link | For highly toxic plants, the system shall provide a one-tap link to local poison control services. | Medium |
| FR-TOXI-004 | Toxic Plant Database | The system shall maintain a curated database of at least 5,000 toxic plant species with periodic expert review. | High |
| FR-TOXI-005 | Household Safety Scan | The system shall allow users to scan multiple plants in their home and generate a household toxicity safety report. | Low |

### 2.7 Plant Collection Management

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-COLL-001 | Add to Collection | The system shall allow users to save any identified plant to their personal collection with a custom nickname and location tag. | High |
| FR-COLL-002 | Collection Dashboard | The system shall display all collected plants in a grid/list view with thumbnail, name, and health status indicator. | High |
| FR-COLL-003 | Plant Detail Page | Each collection entry shall show identification data, care schedule, diagnosis history, and photos over time. | High |
| FR-COLL-004 | Wishlist | The system shall allow users to add plants to a wishlist with notes on desired purchase source and price range. | Medium |
| FR-COLL-005 | Remove / Archive Plant | Users shall be able to remove or archive plants from their collection with a confirmation dialog. | Medium |
| FR-COLL-006 | Search & Filter | The system shall support searching and filtering the collection by name, species, location, or health status. | Medium |
| FR-COLL-007 | Export Collection | The system shall allow users to export their collection data as CSV or PDF. | Low |

### 2.8 Plant Recommendation

| ID | Title | Description | Priority |
|---|---|---|---|
| FR-RECO-001 | Skill-Based Filtering | The system shall recommend plants matched to the user's self-reported gardening experience (beginner, intermediate, expert). | High |
| FR-RECO-002 | Space-Based Filtering | The system shall tailor recommendations based on available space (indoor, balcony, small garden, large garden). | High |
| FR-RECO-003 | Climate Awareness | The system shall factor the user's location climate zone into recommendations. | Medium |
| FR-RECO-004 | Pet-Safe Filter | The system shall provide a toggle to exclude all toxic-to-pets species from recommendations. | High |
| FR-RECO-005 | Purpose Filter | Users shall filter recommendations by purpose: ornamental, edible, air-purifying, or medicinal. | Medium |
| FR-RECO-006 | Recommendation Detail | Each recommendation shall include species info, difficulty rating, care summary, and a link to add to wishlist. | Medium |

---

## 3. Non-Functional Requirements

### 3.1 Security

| ID | Title | Metric / Description | Priority |
|---|---|---|---|
| NFR-SEC-001 | Data Encryption in Transit | All API traffic shall use TLS 1.2+ with HSTS enforced. | High |
| NFR-SEC-002 | Data Encryption at Rest | User data and images shall be encrypted using AES-256 at rest. | High |
| NFR-SEC-003 | Authentication Token Security | JWT tokens shall expire after 1 hour; refresh tokens after 30 days. | High |
| NFR-SEC-004 | OWASP Compliance | The application shall pass OWASP Mobile Top 10 audit with zero critical findings. | High |
| NFR-SEC-005 | GDPR / Privacy Compliance | The system shall support data export, deletion, and consent management per GDPR and CCPA. | High |

### 3.2 Performance

| ID | Title | Metric / Description | Priority |
|---|---|---|---|
| NFR-PERF-001 | Identification Latency | Plant identification shall return results within 3 seconds (p95) on a 4G connection. | High |
| NFR-PERF-002 | Disease Diagnosis Latency | Disease diagnosis shall return results within 4 seconds (p95). | High |
| NFR-PERF-003 | App Cold Start | The application shall launch to the home screen within 2 seconds on mid-range devices. | Medium |
| NFR-PERF-004 | Image Upload Size | The system shall accept images up to 20 MB; images shall be compressed client-side to ≤ 2 MB before upload. | Medium |

### 3.3 Availability & Scalability

| ID | Title | Metric / Description | Priority |
|---|---|---|---|
| NFR-AVL-001 | Uptime SLA | The backend services shall maintain 99.9% monthly uptime. | High |
| NFR-AVL-002 | Horizontal Scaling | The identification service shall auto-scale to handle up to 10,000 concurrent identification requests. | High |
| NFR-AVL-003 | Graceful Degradation | If the AI service is unavailable, the app shall display cached results and queue new requests. | Medium |

### 3.4 Usability

| ID | Title | Metric / Description | Priority |
|---|---|---|---|
| NFR-USB-001 | Accessibility | The app shall comply with WCAG 2.1 Level AA guidelines. | High |
| NFR-USB-002 | Localization | The app shall support at least 10 languages at launch including English, Spanish, Chinese, French, and German. | Medium |
| NFR-USB-003 | Onboarding | New users shall complete onboarding and their first identification in under 60 seconds. | Medium |

### 3.5 Maintainability

| ID | Title | Metric / Description | Priority |
|---|---|---|---|
| NFR-MNT-001 | Code Coverage | Automated unit test coverage shall be ≥ 80% for business logic modules. | Medium |
| NFR-MNT-002 | API Versioning | All public APIs shall follow semantic versioning with at least one prior version supported. | Medium |
| NFR-MNT-003 | CI/CD Pipeline | Builds, tests, and deployments shall be fully automated with rollback capability within 15 minutes. | Medium |

---

## 4. Technical Constraints & Integration

| ID | Constraint / Integration | Details |
|---|---|---|
| TC-001 | Mobile Platforms | iOS 15+ and Android 10+ shall be supported. |
| TC-002 | Camera API | The app shall integrate with native camera APIs (AVFoundation on iOS, CameraX on Android). |
| TC-003 | ML Model Hosting | The PIE and DDE models shall be hosted on a GPU-accelerated cloud service (e.g., AWS SageMaker or GCP Vertex AI). |
| TC-004 | Push Notification Service | Firebase Cloud Messaging (Android) and APNs (iOS) shall deliver reminder notifications. |
| TC-005 | Chat Infrastructure | Real-time expert chat shall use WebSocket connections with a fallback to long-polling. |
| TC-006 | Light Sensor API | The light meter shall use the device's ambient light sensor via platform-native sensor APIs. |
| TC-007 | Image Storage | User-uploaded images shall be stored in cloud object storage (e.g., AWS S3) with lifecycle policies archiving images older than 24 months. |
| TC-008 | Database | Structured data shall be stored in a managed relational database (e.g., PostgreSQL); plant metadata in a search-optimized store (e.g., Elasticsearch). |

---

## 5. User Personas & Use Cases

### 5.1 Personas

| Persona | Description | Goals |
|---|---|---|
| **Casual Walker (Clara)** | A nature enthusiast who photographs plants during walks. Low tech skill. | Quick identification, learn plant facts. |
| **Parent Educator (Paul)** | A parent wanting to teach children about plants and keep them safe from toxic species. | Safe identification, toxicity alerts, kid-friendly info. |
| **Home Gardener (Hana)** | An intermediate gardener maintaining 15+ indoor plants. | Care reminders, disease diagnosis, collection tracking. |
| **Professional Botanist (Ben)** | An expert seeking quick field identification and disease verification. | High-accuracy ID, expert chat for second opinions. |

### 5.2 Use Cases

| UC ID | Title | Actor | Precondition | Main Flow | Postcondition |
|---|---|---|---|---|---|
| UC-001 | Identify a Plant | Clara | App installed, camera permission granted. | 1. Open app → 2. Tap "Identify" → 3. Capture photo → 4. View top-3 results → 5. Select correct match → 6. Save to collection. | Plant saved with species data. |
| UC-002 | Diagnose Disease | Hana | Plant in collection shows symptoms. | 1. Open plant detail → 2. Tap "Diagnose" → 3. Photograph affected area → 4. View diagnosis & severity → 5. Follow treatment steps. | Diagnosis logged; treatment plan displayed. |
| UC-003 | Set Care Reminder | Hana | Plant exists in collection. | 1. Open plant detail → 2. Tap "Reminders" → 3. Configure watering schedule → 4. Save. | Recurring notification scheduled. |
| UC-004 | Consult Expert | Paul | User authenticated. | 1. Navigate to "Expert" tab → 2. Select available expert → 3. Describe issue with photo → 4. Receive advice → 5. Rate expert. | Chat transcript saved; expert rated. |
| UC-005 | Check Toxicity | Paul | Identification completed. | 1. View identification result → 2. See toxicity badge → 3. Tap for details → 4. View affected groups and emergency contacts. | User informed of risks. |
| UC-006 | Get Recommendations | Clara | Profile with skill level and space set. | 1. Navigate to "Discover" → 2. Set filters (skill, space, pet-safe) → 3. Browse recommendations → 4. Add to wishlist. | Wishlist updated. |

---

## 6. Edge Cases, Risks, and Gaps

| ID | Category | Description | Mitigation |
|---|---|---|---|
| RISK-001 | Accuracy | PIE may misidentify visually similar species (e.g., edible vs. toxic look-alikes). | Display confidence scores; prompt user verification for scores < 85%; add disclaimer. |
| RISK-002 | Connectivity | Users in remote areas may lack network access during identification. | FR-IDEN-007 offline queuing; consider an on-device lite model for top-500 species. |
| RISK-003 | Expert Availability | Peak hours may exceed expert capacity, violating the 5-min SLA. | Implement AI-assisted triage bot for common queries; queue management with ETA display. |
| RISK-004 | Image Quality | Blurry, low-light, or partial images degrade accuracy. | Provide real-time camera guidance (framing, focus, lighting tips) before capture. |
| RISK-005 | Data Privacy | Plant photos may contain GPS metadata exposing user location. | Strip EXIF metadata on upload unless user explicitly opts in for location tagging. |
| RISK-006 | Liability | Incorrect toxicity classification could endanger users. | Add legal disclaimer; require expert review for toxicity database changes; log all toxicity queries for audit. |
| GAP-001 | Monetization | Subscription tiers, in-app purchases, and ad strategy are not yet defined. | Defer to a separate business requirements document. |
| GAP-002 | Admin Panel | Expert management, content moderation, and analytics dashboards are not specified. | Plan as a Phase-2 deliverable. |

---

## 7. Assumptions

| ID | Assumption |
|---|---|
| ASM-001 | Users grant camera and notification permissions; features degrade gracefully if denied. |
| ASM-002 | A reliable third-party or in-house ML model exists that meets the 98% accuracy target for 17,000+ species. |
| ASM-003 | A pool of at least 20 qualified experts is available at launch to cover 24/7 consultation. |
| ASM-004 | The curated plant and disease databases are available and licensed for commercial use. |
| ASM-005 | Users have devices with ambient light sensors for the light meter feature. |
| ASM-006 | Internet connectivity is available for core AI features; offline mode is limited to queuing. |

---

## 8. Traceability Matrix

| Requirement ID | Use Case(s) | Persona(s) | Test Strategy |
|---|---|---|---|
| FR-IDEN-001 | UC-001 | Clara, Paul, Hana, Ben | Integration test: camera capture → API call → result display. |
| FR-IDEN-003 | UC-001 | All | Benchmark test on labeled dataset; accuracy ≥ 98%. |
| FR-DIAG-003 | UC-002 | Hana, Ben | Benchmark test on disease image dataset; coverage ≥ 500 diseases. |
| FR-DIAG-005 | UC-002 | Hana | Manual QA review of treatment text for 50 sample diseases. |
| FR-CARE-002 | UC-003 | Hana | Automated test: create reminder → verify notification fires at scheduled time. |
| FR-CARE-008 | UC-003 | Hana | Device test: compare light meter reading against reference lux meter (±15% tolerance). |
| FR-EXPT-001 | UC-004 | Paul, Ben | End-to-end test: initiate chat → send message → receive expert reply < 5 min. |
| FR-EXPT-003 | UC-004 | Paul | Load test: simulate 100 concurrent chat sessions; verify routing and response SLA. |
| FR-TOXI-001 | UC-005 | Paul | Unit test: identification of known toxic species triggers warning badge. |
| FR-TOXI-003 | UC-005 | Paul | Manual QA: verify emergency link resolves to correct regional poison control number. |
| FR-RECO-001 | UC-006 | Clara | Functional test: set skill=beginner → verify only easy-care plants returned. |
| FR-RECO-004 | UC-006 | Paul | Functional test: enable pet-safe toggle → verify no toxic-to-pets species in results. |
| FR-COLL-001 | UC-001 | All | Integration test: save plant → verify appears in collection dashboard. |
| NFR-PERF-001 | UC-001 | All | Performance test: 1,000 concurrent ID requests; p95 latency ≤ 3 s. |
| NFR-SEC-005 | — | All | Compliance audit: verify data export and deletion flows per GDPR checklist. |
