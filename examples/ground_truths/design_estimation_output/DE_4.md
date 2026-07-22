# Design & Estimation — Movie Seat Booking Application

| Field           | Detail                        |
|-----------------|-------------------------------|
| **Document ID** | DE-MSB-2026-04                |
| **Version**     | 1.0                           |
| **Date**        | 2026-04-27                    |
| **Based On**    | SRS-MSB-2026-04 v1.0 (RA_4)  |
| **Author**      | Design & Estimation Team      |
| **Status**      | Draft                         |

---

## 1. Executive Summary

This document translates **SRS-MSB-2026-04** into a concrete design, technology selection, and delivery estimate for the Movie Seat Booking (MSB) application. MSB is a client-side SPA that lets users pick a movie, select seats on a visual theater map, view a real-time price total, and have their choices persisted via the browser's `localStorage` API (FR-PER-001 – FR-PER-005). No back-end, authentication, or payment integration is in scope (TC-001 – TC-004). The design emphasizes performance (NFR-PERF-001 ≤ 1.5 s FCP), accessibility (NFR-USA-002 WCAG AA), offline operation (NFR-AVL-001), and future extensibility toward a server-backed booking system (RSK-003).

---

## 2. System Architecture

### 2.1 Pattern Justification

A **Component-Based Client-Side SPA** pattern is selected. All requirements (TC-001, TC-002) mandate a pure front-end with no server-side logic. The architecture separates concerns into four layers (NFR-SCA-003): Data Model, State/Persistence, Rendering, and Event Handling.

### 2.2 Architecture Diagram

```
┌─────────────────────────────────────────────────────┐
│                     Browser                         │
│                                                     │
│  ┌──────────┐  ┌────────────┐  ┌────────────────┐  │
│  │  Movie    │  │  Seat Map  │  │  Cost Summary  │  │
│  │ Selector  │  │  Grid      │  │  Panel         │  │
│  └────┬─────┘  └─────┬──────┘  └───────┬────────┘  │
│       │              │                  │           │
│  ┌────▼──────────────▼──────────────────▼────────┐  │
│  │            State Manager (AppState)           │  │
│  └────────────────────┬──────────────────────────┘  │
│                       │                             │
│  ┌────────────────────▼──────────────────────────┐  │
│  │        Persistence Layer (localStorage)       │  │
│  │        Keys: msb_movieIndex, msb_seats        │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  ┌───────────────────────────────────────────────┐  │
│  │  Static Assets (HTML · CSS · JS) served via   │  │
│  │  CDN / any HTTP server  (TC-005)              │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

### 2.3 Component List

| Component         | Responsibility                                      | RA Refs                               |
|-------------------|-----------------------------------------------------|---------------------------------------|
| **MovieSelector** | Dropdown rendering, price display, change events     | FR-MOV-001 – FR-MOV-006              |
| **SeatMap**       | Grid rendering, seat state visualization, legend     | FR-SEAT-001 – FR-SEAT-008            |
| **SeatToggle**    | Click/keyboard handlers, selection logic, limit      | FR-SEL-001 – FR-SEL-007              |
| **CostSummary**   | Real-time count & total price display                | FR-PRC-001 – FR-PRC-004              |
| **StateManager**  | Central app state, event bus                         | NFR-SCA-003                           |
| **StorageAdapter**| localStorage read/write, validation, namespace       | FR-PER-001 – FR-PER-006, NFR-SEC-002 |

---

## 3. Technology Stack

### 3.1 Frontend

| Technology        | Version | Justification                                                              |
|-------------------|---------|----------------------------------------------------------------------------|
| **HTML 5**        | —       | Semantic markup, accessibility attributes (FR-SEL-007)                     |
| **CSS 3 / Flexbox + Grid** | — | Responsive seat grid (FR-SEAT-008), color states (FR-SEAT-002–004) |
| **Vanilla JS (ES2022)** | — | TC-001 mandates no heavy framework; keeps bundle ≤ 150 KB (NFR-PERF-004) |
| **Vite**          | 5.x     | Dev server + production bundler; tree-shaking for minimal output           |

### 3.2 Tooling & Quality

| Tool              | Version | Purpose                                         |
|-------------------|---------|--------------------------------------------------|
| **ESLint**        | 9.x     | Linting, code style enforcement                  |
| **Prettier**      | 3.x     | Consistent formatting                            |
| **Vitest**        | 2.x     | Unit tests for state, pricing, storage logic     |
| **Playwright**    | 1.x     | E2E tests for seat toggle, persistence, UI       |
| **Lighthouse CI** | 0.14.x  | Performance & accessibility audits (NFR-PERF-001)|

### 3.3 Database

Not applicable — all persistence uses the browser Web Storage API (TC-002). Data schema is detailed in §6.

---

## 4. Infrastructure & Deployment

| Aspect              | Choice                                                 | RA Ref          |
|---------------------|--------------------------------------------------------|-----------------|
| **Hosting**         | Static CDN (e.g., Cloudflare Pages or Netlify Free)    | TC-005          |
| **CI/CD**           | GitHub Actions: lint → test → build → deploy           | NFR-SCA-003     |
| **Environments**    | `dev` (local Vite), `staging` (preview branch), `prod` | —               |
| **Offline Support** | Service Worker with cache-first strategy               | NFR-AVL-001     |
| **TLS**             | Enforced via CDN (HTTPS only)                          | NFR-SEC-001     |

### 4.1 CI/CD Pipeline

```
push → lint (ESLint/Prettier) → unit tests (Vitest)
     → build (Vite) → Lighthouse CI audit
     → deploy preview (staging) → manual approve → deploy prod
```

---

## 5. Data Flows for Key Use Cases

### 5.1 UC-001 — Select Movie & Seats

| Step | Actor  | Action                                      | System Response                                                        |
|------|--------|---------------------------------------------|------------------------------------------------------------------------|
| 1    | User   | Selects movie from dropdown                 | StateManager updates `movieIndex`; fires change event                  |
| 2    | System | —                                           | SeatMap re-renders with seat states for the new movie (FR-MOV-006)     |
| 3    | System | —                                           | CostSummary resets to $0.00 / 0 seats (FR-PRC-003)                    |
| 4    | System | —                                           | StorageAdapter writes `msb_movieIndex` (FR-PER-001)                   |
| 5    | User   | Clicks available seat(s)                    | SeatToggle toggles state to "selected" (FR-SEL-001)                   |
| 6    | System | —                                           | CostSummary recalculates total (FR-PRC-001); StorageAdapter persists `msb_seats` (FR-PER-002) |

### 5.2 UC-003 — Resume Previous Session

| Step | Actor  | Action         | System Response                                                                          |
|------|--------|----------------|------------------------------------------------------------------------------------------|
| 1    | User   | Opens app URL  | `DOMContentLoaded` fires                                                                 |
| 2    | System | —              | StorageAdapter reads `msb_movieIndex` and `msb_seats`                                    |
| 3    | System | —              | Validates data against schema (FR-PER-004, NFR-SEC-002); falls back to defaults on error |
| 4    | System | —              | StateManager hydrates state; MovieSelector, SeatMap, CostSummary render restored view    |

---

## 6. Database Schema Overview

Since persistence is `localStorage` (TC-002), the "schema" is a set of key-value pairs:

| Key                | Type         | Example Value        | Validated Against              | RA Ref                     |
|--------------------|-------------|----------------------|--------------------------------|----------------------------|
| `msb_movieIndex`   | Number      | `1`                  | Integer ≥ 0, < catalog length  | FR-PER-001, FR-PER-005    |
| `msb_seats`        | JSON Array  | `[2,5,14]`           | Array of integers, each ≥ 0, < totalSeats, unique, length ≤ maxLimit | FR-PER-002, FR-SEL-005 |

### In-Memory Data Model

```js
const appConfig = {
  rows: 6,            // NFR-SCA-001 — configurable
  cols: 8,            // NFR-SCA-001
  maxSelectableSeats: 10,  // FR-SEL-005
  movies: [           // NFR-SCA-002 — single array to extend
    { title: "Avengers: Endgame",  price: 10 },
    { title: "Joker",              price: 12 },
    { title: "Toy Story 4",        price: 8  },
  ],
  occupiedSeats: [3, 11, 22, 37, 41],  // ASM-002
};
```

---

## 7. API Surface Overview

No REST/GraphQL API exists (TC-002). The internal programmatic interface is documented below for developer guidance:

| Module          | Function / Method            | Signature                              | Description                         |
|-----------------|------------------------------|----------------------------------------|-------------------------------------|
| StateManager    | `getState()`                 | `() → AppState`                        | Returns current immutable snapshot  |
| StateManager    | `dispatch(action)`           | `(Action) → void`                      | Updates state, notifies subscribers |
| StorageAdapter  | `load()`                     | `() → Partial<AppState> \| null`       | Reads & validates localStorage      |
| StorageAdapter  | `save(state)`                | `(AppState) → void`                    | Writes current state to storage     |
| SeatMap         | `render(state)`              | `(AppState) → void`                    | Draws seat grid to DOM              |
| CostSummary     | `update(count, price)`       | `(number, number) → void`             | Refreshes displayed totals          |

---

## 8. Development Effort Estimation

### 8.1 Module T-Shirt Sizing

| Module                | Size | Estimated Hours | RA Refs                           |
|-----------------------|------|----------------:|-----------------------------------|
| Project Setup & Build | S    | 4               | TC-001, TC-005                    |
| Movie Selector        | S    | 6               | FR-MOV-001 – FR-MOV-006          |
| Seat Map Rendering    | M    | 12              | FR-SEAT-001 – FR-SEAT-008        |
| Selection Logic       | M    | 10              | FR-SEL-001 – FR-SEL-007          |
| Cost Summary          | S    | 4               | FR-PRC-001 – FR-PRC-004          |
| Persistence Layer     | S    | 6               | FR-PER-001 – FR-PER-006          |
| Service Worker        | S    | 4               | NFR-AVL-001                       |
| Styling & Responsive  | M    | 10              | FR-SEAT-008, NFR-USA-001–003     |
| Accessibility         | S    | 6               | FR-SEL-007, NFR-USA-002–004      |
| Testing (Unit + E2E)  | M    | 14              | All FRs, NFR-PERF-001            |
| CI/CD & Deploy        | S    | 4               | TC-005                            |
| **Total**             |      | **80 h**        |                                   |

### 8.2 Phased Delivery

| Phase | Duration   | Deliverables                                        |
|-------|------------|-----------------------------------------------------|
| 1     | Week 1     | Setup, Movie Selector, Seat Map grid rendering      |
| 2     | Week 2     | Selection logic, Cost Summary, Persistence layer    |
| 3     | Week 3     | Responsive styling, Accessibility, Service Worker   |
| 4     | Week 4     | Testing, CI/CD, Performance audit, Deploy to prod   |

### 8.3 Team Composition

| Role                   | Count | Allocation |
|------------------------|------:|-----------:|
| Front-End Developer    | 1     | 100 %      |
| UI/UX Designer         | 1     | 25 % (Phases 1–3) |
| QA Engineer            | 1     | 50 % (Phases 3–4)  |

---

## 9. Technical Risks & Mitigation

| Risk ID | Description                                     | Likelihood | Impact | Mitigation                                                           | RA Ref   |
|---------|-------------------------------------------------|------------|--------|----------------------------------------------------------------------|----------|
| RSK-001 | localStorage tampered via DevTools               | Medium     | Low    | Schema validation on read; graceful fallback (FR-PER-004)            | RSK-001  |
| RSK-002 | Seat grid unusable on screens < 320 px           | Low        | Medium | Horizontal scroll wrapper + pinch-zoom on mobile (FR-SEAT-008)      | RSK-002  |
| RSK-003 | Future back-end requirement breaks data model    | High       | High   | Design state shape as serializable DTOs; abstract persistence layer  | RSK-003  |
| RSK-004 | Service Worker caching stale JS after deploy     | Medium     | Medium | Versioned cache names; skip-waiting on new SW activation             | NFR-AVL-001 |
| RSK-005 | Rapid seat toggling causes race conditions       | Low        | Low    | Synchronous state updates; debounce storage writes (EC-005)          | EC-005   |

---

## 10. Security Architecture

| Control                  | Implementation                                                     | RA Ref      |
|--------------------------|--------------------------------------------------------------------|-------------|
| **XSS Prevention**       | Use `textContent` / template literals — no `innerHTML` with user data | NFR-SEC-001 |
| **Storage Validation**   | JSON Schema check on `load()`; reject & reset on mismatch          | NFR-SEC-002 |
| **Content Security Policy** | `script-src 'self'; style-src 'self'; default-src 'none'` header | NFR-SEC-001 |
| **HTTPS Enforcement**    | CDN redirect HTTP → HTTPS; HSTS header                            | TC-005      |
| **Dependency Audit**     | `npm audit` in CI pipeline; zero-high-severity policy              | —           |

---

## 11. Monitoring & Observability

| Aspect               | Tool / Approach                                   | Purpose                                     |
|-----------------------|---------------------------------------------------|---------------------------------------------|
| **Performance**       | Lighthouse CI in pipeline                         | Catch FCP / bundle regressions (NFR-PERF-001, NFR-PERF-004) |
| **Error Tracking**    | `window.onerror` + `unhandledrejection` listener → console (prod: optional Sentry free tier) | Runtime error visibility |
| **Analytics (opt-in)**| Privacy-friendly beacon (e.g., Plausible)         | Track movie popularity, seat selection count |
| **Uptime**            | CDN built-in health checks                        | Verify static asset availability             |

---

## 12. Cost Estimation

All figures are monthly USD estimates for the production environment.

| Item                       | Service                | Estimated Cost |
|----------------------------|------------------------|---------------:|
| Hosting (CDN)              | Cloudflare Pages Free  | $0             |
| Domain Name                | Annual ÷ 12           | ~$1            |
| CI/CD                      | GitHub Actions Free    | $0             |
| Error Tracking (optional)  | Sentry Free Tier       | $0             |
| Analytics (optional)       | Plausible Community    | $0             |
| **Total Monthly**          |                        | **~$1**        |

> The application is a static SPA with no back-end services (TC-002), resulting in near-zero operating costs.

---

## 13. Recommendations & Next Steps

1. **Confirm movie catalog** — Finalize titles, prices, and occupied-seat data before Phase 1 build (ASM-001, ASM-002).
2. **Design mockups** — Produce high-fidelity seat map and responsive layouts to validate against NFR-USA-001 and FR-SEAT-008 before coding.
3. **Accessibility audit** — Schedule a manual WCAG audit in Phase 3 to close the identified gap (§6.3 of RA).
4. **Back-end readiness** — Abstract the `StorageAdapter` behind an interface so it can be swapped to a REST client in a future release (RSK-003).
5. **Localization groundwork** — Use a string constants file for UI copy to ease future i18n (identified gap in RA §6.3).
6. **Stakeholder demo** — Plan an end-of-Phase-2 demo to validate core booking flow before investing in polish.

---

## 14. Traceability Matrix

| RA Requirement   | Design Component(s)                | DE Section | Estimation Phase |
|------------------|------------------------------------|-----------|------------------|
| FR-MOV-001–006   | MovieSelector, StateManager        | §2.3, §5.1| Phase 1          |
| FR-SEAT-001–008  | SeatMap                            | §2.3, §6  | Phase 1          |
| FR-SEL-001–007   | SeatToggle, StateManager           | §2.3, §5.1| Phase 2          |
| FR-PRC-001–004   | CostSummary                        | §2.3, §5.1| Phase 2          |
| FR-PER-001–006   | StorageAdapter                     | §2.3, §6  | Phase 2          |
| NFR-PERF-001–004 | Vite build, Lighthouse CI          | §3, §11   | Phase 4          |
| NFR-USA-001–004  | CSS, Accessibility pass            | §3, §8.1  | Phase 3          |
| NFR-AVL-001–003  | Service Worker, Fallback logic     | §4, §8.1  | Phase 3          |
| NFR-SEC-001–002  | CSP, StorageAdapter validation     | §10       | Phase 2–3        |
| NFR-SCA-001–003  | appConfig, Module structure        | §6, §7    | Phase 1          |
| TC-001–TC-005    | Overall architecture & deployment  | §2, §4    | Phase 1, 4       |
| EC-001–EC-006    | StorageAdapter, SeatToggle guards  | §5.2, §9  | Phase 2–3        |
| RSK-001–RSK-003  | Validation, Responsive, Abstraction| §9        | All              |

---

*End of Document — DE-MSB-2026-04 v1.0*
