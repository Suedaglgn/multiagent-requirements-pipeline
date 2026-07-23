# Design & Estimation (DE) Document  
*Generated from Approved Requirements Analysis (RA‑001)*  

---  

## 1. Executive Summary  

The **Movie Seat Selector** web application must enable users to **browse movies (FR‑UI‑001)**, **select seats (FR‑SEAT‑001‑FR‑SEAT‑005)**, **calculate a total cost (FR‑CALC‑001‑FR‑CALC‑005)**, **persist selections in local storage (FR‑PERSIST‑001‑FR‑PERSIST‑006)**, and meet **non‑functional metrics** such as performance (NFR‑PERF‑001, NFR‑PERF‑002), security (NFR‑SEC‑001, NFR‑SEC‑002), availability (NFR‑AVL‑001), scalability (NFR‑SCA‑001), usability (NFR‑USU‑001), and maintainability (NFR‑MNT‑001).  

All functional requirements (FR‑…) are traceable to design decisions (see Traceability Matrix, Section 14). The design leverages client‑side processing (TF‑002) and modern web standards to satisfy technical constraints (TF‑001‑TF‑007).  

---  

## 2. System Architecture  

### 2.1 Pattern Justification  

| Pattern | RA Reference | Why Used |
|---------|--------------|----------|
| **MVC** | FR‑UI‑001, FR‑SEAT‑002, FR‑CALC‑001 | Separates UI (MVC) from business logic (Model). |
| **Event‑Driven UI** | FR‑SEAT‑005, FR‑CALC‑002 | UI updates instantly on seat selection. |
| **State‑Management (localStorage)** | FR‑PERSIST‑001, FR‑PERSIST‑002 | Keeps selections across reloads. |
| **Caching** | NFR‑PERF‑001 (≤ 200 ms) | Seat‑price lookup cached per user session. |
| **Responsive Layout** | TF‑003 | Grid‑based seat map adapts to screen size. |

### 2.2 ASCII Diagram  

```
+-------------------+          +-------------------+          +-------------------+
|  Browser UI       |<---http->|  Front‑end (JS)   |<---http->|  Back‑end API    |
|  (HTML/CSS)       |          |  - Seat Map UI   |          |  - Seat availability|
|  localStorage     |<---http->|  - Cost Calc UI  |          |    endpoint       |
|  (key: movieSelection) |          |  - Error UI    |          +-------------------+
+-------------------+          +-------------------+                   
        ^                                 ^                               ^
        |                                 |                               |
        |                                 |                               |
        |                                 |                               |
        |                                 |                               |
        |                                 |                               |
        v                                 v                               v
   Page Load                              API Call                           API Call (static data)
```

### 2.3 Component List  

| Component | Responsibility | RA Reference |
|-----------|----------------|--------------|
| **UI Layer** | Render movie list, seat map, cost panel | FR‑UI‑001, FR‑SEAT‑001‑FR‑SEAT‑005, FR‑CALC‑001‑FR‑CALC‑005 |
| **Logic Layer** | Compute total cost, validate seat availability | FR‑CALC‑001, FR‑SEAT‑004 |
| **Persistence Layer** | Store/load selections in localStorage | FR‑PERSIST‑001‑FR‑PERSIST‑006 |
| **State Machine** | Manage selection lifecycle (open → selected → cancelled) | FR‑UI‑002, FR‑UI‑005 |
| **Error Handler** | Show “Sold out”, “No seats selected” | FR‑UI‑004, FR‑CALC‑004 |

---  

## 3. Technology Stack  

### 3.1 Front‑end  

| Technology | Version | Justification |
|------------|---------|---------------|
| **HTML5** | N/A | Core markup for UI. |
| **CSS3** | 1.0 (Flexbox/Grid) | Responsive seat‑map (TF‑003). |
| **JavaScript (ES2023)** | 2023.09 | Modern APIs (`localStorage`, `ResizeObserver`). |
| **React (v18)** | 18.2.0 | Componentized UI, easy state management. |
| **React‑Router v6** | 6.22.0 | Navigate between “browse”, “select”, “confirm” flows. |
| **Lodash (v4.17.21)** | 4.17.21 | Utility functions for seat IDs. |
| **Testing** | Jest + React Testing Library | Unit & integration tests (≥ 80 % coverage, TF‑007). |

### 3.2 Back‑end  

| Technology | Version | Justification |
|------------|---------|---------------|
| **Node.js (v18.x)** | 18.19.0 | Single‑file backend for API & static seat map. |
| **Express (v4.19.2)** | 4.19.2 | Minimalist routing, middleware support. |
| **CORS** | 2.8.5 | Secure cross‑origin (HTTPS only). |
| **dotenv** | 10.0.1 | Environment variable management. |
| **Static Data** | In‑memory JSON file (`seats.json`) | Meets TF‑001 (client‑side data). |
| **Logging** | Pino | Lightweight JSON logs for monitoring (NFR‑AVL‑001). |

### 3.3 Database  

| Table | Version | Columns | Purpose | RA Reference |
|-------|---------|---------|---------|--------------|
| `movies` | 1.0 | `id (PK)`, `title`, `width`, `height` | Stores static movie metadata; fetched on load (A‑001). | FR‑UI‑001 |
| `seats` | 1.0 | `id (PK)`, `movie_id (FK)`, `row`, `col`, `status` (`open`/`taken`) | Seat map; status updated during selection (FR‑SEAT‑002). | FR‑SEAT‑002 |
| `selections` | 1.0 | `user_id`, `movie_id`, `selected_seat_ids (JSON)`, `selected_at (ISO8601)` | Persists selections in DB for audit (optional). | FR‑PERSIST‑001‑FR‑PERSIST‑002 |
| `discounts` | 1.0 | `code`, `value_percent` | Drives cost calculation (FR‑CALC‑005). | FR‑CALC‑005 |

---  

## 4. Infrastructure & Deployment  

| Area | Service | Configuration | RA Reference |
|------|---------|---------------|--------------|
| **Cloud Provider** | AWS EC2 (t3.medium) + RDS (PostgreSQL) | Auto‑scaling, daily backups | NFR‑AVL‑001 |
| **CI/CD** | GitHub Actions | Build → unit tests → deploy to `staging` → `prod` | TF‑007 (80 % coverage) |
| **Environments** | `dev` (local), `staging` (AWS), `prod` (AWS) | Feature flags to disable payment later (A‑006) | FR‑PERSIST‑004 |
| **Monitoring** | CloudWatch + Grafana | Alerts on latency > 200 ms (NFR‑PERF‑001) | NFR‑PERF‑001 |
| **Security** | HTTPS (Let’s Encrypt), CSP, SameSite cookies | Prevent PII leakage (NFR‑SEC‑001) | NFR‑SEC‑001 |

---  

## 5. Data Flows for Key Use Cases  

### 5.1 UC‑001 – Browse & Select  

| Step | Action | RA Link | Flow Details |
|------|--------|---------|--------------|
| 1 | Page loads → `movies.json` fetched → UI renders list | A‑001, FR‑UI‑001 | Server sends static list; client renders. |
| 2 | User clicks movie → `movieSelection` key updated with `movie_id` | FR‑UI‑002, FR‑UI‑003 | UI state changed; localStorage writes. |
| 3 | Seat map generated (TF‑004) → grid of `seats` rows/cols | TF‑004 | Each seat element gets `data-seat-id`. |
| 4 | User clicks seat → `seatSelect` event → `selections` array added | FR‑SEAT‑001‑FR‑SEAT‑005 | Timestamp stored; localStorage persists. |
| 5 | Cost recalculated → displayed panel | FR‑CALC‑001‑FR‑CALC‑005 | UI updates instantly; ≤ 200 ms (NFR‑PERF‑001). |
| 6 | User closes tab → browser unloads → localStorage remains | FR‑PERSIST‑001 | Selections survive reload (FR‑PERSIST‑002). |

### 5.2 UC‑002 – Persist Across Reload  

| Step | Action | RA Link | Flow Details |
|------|--------|---------|--------------|
| 1 | `DOMContentLoaded` triggers `loadSelection()` | FR‑PERSIST‑003 | Reads `movieSelection` → restores UI. |
| 2 | If `movieSelection` missing → shows “Select a movie” | FR‑UI‑002 | Graceful fallback. |
| 3 | On “Clear Selections” button → `localStorage.removeItem('movieSelection')` | FR‑PERSIST‑005 | UI & storage cleared. |
| 4 | If storage quota exceeded → fallback to `sessionStorage` | TF‑006 | UI shows “Data persistence temporarily disabled”. |

### 5.3 UC‑003 – Concurrent Conflict Prevention  

| Step | Action | RA Link | Flow Details |
|------|--------|---------|--------------|
| 1 | Two users click seat within 2 s → `concurrentSelect` event fired | FR‑SEAT‑005 | UI disables seat, logs `conflict` event. |
| 2 | Server receives conflict (if any) → returns `seat_taken` status | N/A (static data) | Prevents double‑booking. |
| 3 | Conflict resolved → UI updates to “taken” state | FR‑SEAT‑002 | Consistent view. |

---  

## 6. Database Schema Overview  

```sql
CREATE TABLE movies (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    width INT,
    height INT
);

CREATE TABLE seats (
    id SERIAL PRIMARY KEY,
    movie_id INT REFERENCES movies(id) ON DELETE CASCADE,
    row INT,
    col INT,
    status TEXT NOT NULL CHECK (status IN ('open','taken')) DEFAULT 'open'
);

CREATE TABLE selections (
    id SERIAL PRIMARY KEY,
    movie_id INT REFERENCES movies(id),
    user_id UUID,
    selected_seat_ids TEXT,   -- JSON array
    selected_at TIMESTAMPTZ DEFAULT now()
);
```

*All tables are versioned (1.0) and documented for future maintenance (A‑005).*

---  

## 7. API Surface Overview  

| Endpoint | Method | Path | Purpose | RA Reference |
|----------|--------|------|---------|--------------|
| `GET /movies` | GET | `/movies` | Retrieve static movie list (width/height) | A‑001 |
| `GET /seats` | GET | `/seats?movie_id=X` | Fetch seat map for a movie | FR‑SEAT‑002 |
| `POST /select-seat` | POST | `/select-seat` | Record seat selection (timestamp) | FR‑SEAT‑001, FR‑SEAT‑005 |
| `GET /cost` | GET | `/cost?selected_ids=[A,B]` | Compute total cost (client‑side) – no server needed | FR‑CALC‑001‑FR‑CALC‑005 |
| `GET /clear-selections` | GET | `/clear-selections` | Clear localStorage & DB row (optional) | FR‑PERSIST‑005 |

*All endpoints are stateless and safe for concurrent use (NFR‑SEC‑002).*

---  

## 8. Development Effort Estimation  

| Phase | Module (Feature) | T‑shirt Size | Effort (person‑days) | Owner |
|-------|------------------|--------------|----------------------|-------|
| **Phase 1** | UI + Persistence (FR‑UI‑001, FR‑PERSIST‑001) | Small | 8 | Front‑end 2, QA 1 |
| **Phase 2** | Seat Map + Conflict Handling (FR‑SEAT‑001‑FR‑SEAT‑005) | Medium | 12 | Front‑end 2, Back‑end 1 |
| **Phase 3** | Cost Calculation + UI (FR‑CALC‑001‑FR‑CALC‑005) | Small | 6 | Front‑end 1, QA 1 |
| **Phase 4** | End‑to‑end Testing & Optimization (NFR‑PERF‑001‑NFR‑USU‑001) | Small | 4 | QA 1, DevOps 1 |

**Total**: 30 person‑days ≈ **15 working days** (2‑week sprint).  

**Team Composition**:  
- Front‑end Engineer 1 (Lead)  
- Front‑end Engineer 2  
- Back‑end Engineer 1  
- QA Engineer 1  
- DevOps Engineer 1  

---  

## 9. Technical Risks & Mitigation  

| Risk ID | Description | Probability | Severity | Owner | Mitigation |
|---------|-------------|-------------|----------|-------|------------|
| T‑R001 | LocalStorage quota exceeded (TF‑006) | Medium | High | Front‑end | Implement quota check; fallback to sessionStorage; log warning. |
| T‑R002 | Race condition on concurrent seat selection (FR‑SEAT‑005) | Low | High | Back‑end | Use optimistic UI; server validates seat status; if conflict, reject second selection. |
| T‑R003 | Cost calculation exceeds 200 ms (NFR‑PERF‑001) | Medium | Medium | Front‑end | Cache seat prices per user session; limit DOM updates; performance test early. |
| T‑R004 | UI fails to display “No seats selected” (FR‑CALC‑004) | Low | Medium | Front‑end | Guard clause in cost function; unit test for zero‑cost case. |
| T‑R005 | Storage not cleared on explicit button (FR‑PERSIST‑005) | Very Low | Low | Front‑end | Simple event listener; test with mock storage. |

---  

## 10. Security Architecture  

| Aspect | Control | RA Reference |
|--------|---------|--------------|
| **Transport** | HTTPS only (TLS 1.2+) | A‑007 |
| **Input Validation** | Sanitize seat IDs, prevent injection | NFR‑SEC‑002 |
| **Data at Rest** | No PII stored; only seat IDs (no email) | NFR‑SEC‑001 |
| **Auth** | No login required; session‑aware (A‑004) | FR‑PERSIST‑004 |
| **Static Analysis** | No dangerous code in JS (NFR‑SEC‑001) | NFR‑SEC‑001 |
| **Audit** | Logs of seat selection for compliance | NFR‑SEC‑002 |

---  

## 11. Monitoring & Observability  

| Metric | Source | Threshold | Action |
|--------|--------|-----------|--------|
| Request latency (`/cost`) | CloudWatch | ≤ 200 ms | Alert → investigate performance. |
| HTTP 5xx rate | CloudWatch | > 1 % | Alert → potential backend failure. |
| LocalStorage quota usage | Front‑end | > 95 % | Show fallback UI. |
| Concurrent session count | Front‑end | > 10 | Scale out if needed (NFR‑SCA‑001). |
| Availability | CloudWatch alarms | > 0.1 % downtime | Auto‑restart EC2 instance. |

---  

## 12. Cost Estimation  

| Item | Monthly Cost (USD) | Reason |
|------|--------------------|--------|
| AWS EC2 (t3.medium) | $25 | 1‑month usage, always‑on. |
| RDS PostgreSQL | $20 | 30‑GB storage, 2‑hour read. |
| CDN (static assets) | $5 | CDN for `movies.json`. |
| CI/CD (GitHub Actions) | $15 | 2 GB‑hours. |
| Monitoring (CloudWatch) | $10 | Basic alerts. |
| **Total** | **$75** |  |

*Assuming no payment integration (out‑of‑scope).*

---  

## 13. Recommendations & Next Steps  

1. **Finalize UI mockups** – align with FR‑UI‑001/002.  
2. **Implement Phase 1** (UI + persistence) in Sprint 1 (2 weeks).  
3. **Add seat map logic** (Phase 2) – ensure FR‑SEAT‑005 conflict handling.  
4. **Integrate cost calculation** (Phase 3) – unit‑test FR‑CALC‑004.  
5. **Run performance & security tests** (NFR‑PERF‑001, NFR‑SEC‑001).  
6. **Deploy to staging**, conduct end‑to‑end test cycle.  
7. **Roll out to production** after QA sign‑off.  

---  

## 14. Traceability Matrix  

| Design/Implementation Artifact | RA Functional ID | RA Non‑Functional ID | Verification Method |
|-------------------------------|------------------|----------------------|----------------------|
| UI renders movie list | FR‑UI‑001 | NFR‑USU‑001 | Manual click test, 5 participants |
| Seat map reflects real‑time availability | FR‑SEAT‑002 | NFR‑PERF‑002 | End‑to‑end render ≤ 100 ms |
| Total cost calculation ≤ 200 ms | FR‑CALC‑001 | NFR‑PERF‑001 | Load test 10 concurrent users |
| Selections stored in localStorage | FR‑PERSIST‑001 | NFR‑AVL‑001 | Reload test, 30‑day uptime |
| No PII stored | FR‑PERSIST‑006 | NFR‑SEC‑001 | Static analysis |
| UI clears selections on button | FR‑PERSIST‑005 | NFR‑MNT‑001 | Code review checklist |
| 10 concurrent users → ≤ 30 ms | N/A | NFR‑SCA‑001 | Load test 10 users |
| 80 % code coverage | N/A | NFR‑MNT‑001 | Jest report |
| No login required | FR‑PERSIST‑004 | NFR‑SEC‑002 | Access test, no credentials |

---  

*End of Design & Estimation Document*