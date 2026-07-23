# Software Requirements Specification (SRS)  
**Document ID:** RA‑001  
**Version:** 1.0  
**Date:** 2025‑09‑17  

---  

## 1. Introduction  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑UI‑001 | Users can browse a list of movies. | The application presents a static list of movie titles that the user can scroll through and select. | High |
| FR‑UI‑002 | Users can select available seats for the chosen movie. | When a movie is selected, a seat map becomes interactive and users can click to reserve seats. | High |
| FR‑CALC‑001 | The application calculates the total cost of selected seats. | The total cost is derived from seat price, quantity, and any applicable discounts, and displayed to the user. | High |
| FR‑PERSIST‑001 | The application stores user selections in local storage. | Selections are persisted to the browser’s `localStorage` key “movieSelection”. | High |
| NFR‑SEC‑001 | The application does not store or transmit personally identifiable information (PII). | No user data is collected beyond the selected seats; no login or email is required. | Medium |
| NFR‑PERF‑001 | Total cost calculation must complete within 200 ms on a typical 2.5 GHz laptop. | Measured via performance testing; result ≤ 200 ms under normal conditions. | High |
| NFR‑AVL‑001 | The application remains available to users 99.9 % of the time during normal operation. | Availability measured over a 30‑day sprint; downtime ≤ 0.1 % (≈ 22 minutes). | High |
| NFR‑SCA‑001 | The application can support up to 10 concurrent users without performance degradation. | Scalability tested with 10 users; average load ≤ 30 ms per request. | Medium |
| NFR‑USU‑001 | The application provides an intuitive UI that requires no more than 2 clicks to complete seat selection. | Usability test with 5 participants; average number of clicks ≤ 2. | High |
| NFR‑MNT‑001 | All requirements are documented and maintainable for future developers. | Documentation coverage ≥ 95 %; code comments ≥ 80 %. | Medium |

### 1.1. Purpose  
To define a complete set of functional and non‑functional requirements for the **Movie Seat Selector** web application, ensuring that users can browse movies, select seats, view a clear total cost, and have selections persisted across browser sessions, while adhering to technical constraints and non‑functional performance, security, and usability metrics.

### 1.2. Scope  
- **In‑scope:** User interface, seat selection, cost calculation, local‑storage persistence, error handling for unavailable seats, and basic edge‑case recovery.  
- **Out‑of‑scope:** User authentication, payment processing, movie database integration beyond seat availability, and mobile app development.

### 1.3. Definitions  

| ID | Definition |
|----|------------|
| FR‑AUTH‑001 | “Local storage” refers to the browser API that stores key/value pairs in the browser’s persistent storage (`localStorage` or `sessionStorage`). |
| FR‑AUTH‑002 | “Total cost” is the sum of seat prices multiplied by quantity, minus any discount applied. |
| FR‑AUTH‑003 | “Available seats” are seats whose reservation status is “open” at the moment the user selects them. |
| FR‑AUTH‑004 | “Concurrent selection” occurs when two users attempt to select the same seat within the same 2‑second window. |
| NFR‑AUTH‑001 | “Maintainability” is the ease with which requirements can be understood, modified, and verified by the development team. |

### 1.4. Audience  

| ID | Audience |
|----|----------|
| FR‑AUTH‑005 | Development Team – UI, JS, and backend integrators. |
| FR‑AUTH‑006 | Quality Assurance – testers, performance, and security specialists. |
| FR‑AUTH‑007 | Product Owner – to validate business logic and metrics. |
| FR‑AUTH‑008 | End‑User (Customer) – to ensure the experience meets expectations. |

---  

## 2. Functional Requirements  

### 2.1. Domain: User Interface  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑UI‑001 | Users can browse a list of movies. | A horizontally scrollable list of movie titles is rendered; each title is clickable to indicate selection. | High |
| FR‑UI‑002 | The application displays the current selection of movies. | When no movie is selected, the UI shows “Select a movie”. | High |
| FR‑UI‑003 | The application updates the movie list if the underlying data changes. | On page load, the list is refreshed from the data source within 500 ms. | Medium |
| FR‑UI‑004 | The application validates that the selected movie is still available. | If a movie’s seats are sold out, the UI shows “Sold out – choose another”. | Medium |
| FR‑UI‑005 | The application clears the movie selection when the user clicks “Cancel”. | All stored selections are removed from local storage and UI is reset. | High |

### 2.2. Domain: Seat Selection  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑SEAT‑001 | Users can select individual seats from a seat map. | A 2‑D grid of seats appears; each seat is a clickable element. | High |
| FR‑SEAT‑002 | The seat map reflects real‑time seat availability. | Seats marked “taken” are disabled; “available” seats are enabled. | High |
| FR‑SEAT‑003 | The application records the timestamp of each seat selection. | Timestamps are stored as ISO strings alongside seat IDs. | Medium |
| FR‑SEAT‑004 | The application limits seat selection to the current movie. | Only seats belonging to the selected movie are rendered. | High |
| FR‑SEAT‑005 | The application prevents selection of the same seat twice by the same user within 5 seconds. | UI disables the seat and logs a conflict event. | High |

### 2.3. Domain: Cost Calculation  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑CALC‑001 | The application calculates the total cost of selected seats. | Total = Σ (seat price × quantity) – discount. | High |
| FR‑CALC‑002 | The total cost is displayed in a dedicated cost panel. | The panel updates instantly when seats are added/removed. | High |
| FR‑CALC‑003 | The displayed cost is rounded to the nearest cent. | Implemented using JavaScript `Number.toFixed(2)`. | High |
| FR‑CALC‑004 | If the total cost is zero, the UI shows “No seats selected”. | Handled in the cost calculation function. | High |
| FR‑CALC‑005 | The application validates that the calculated cost matches the sum of displayed seat prices. | Tested by unit tests with edge cases. | High |

### 2.4. Domain: Persistence  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑PERSIST‑001 | The application stores the selection of seats in local storage. | Data stored under key “movieSelection”. | High |
| FR‑PERSIST‑002 | The stored data includes: selected movie ID, array of selected seat IDs, timestamps. | Structured JSON string. | High |
| FR‑PERSIST‑003 | The application loads the stored data when the page loads. | On `DOMContentLoaded`, parse and render selections. | High |
| FR‑PERSIST‑004 | If local storage is unavailable, the application falls back to a “no‑save” state. | UI displays “Data persistence temporarily disabled”. | Medium |
| FR‑PERSIST‑005 | The application clears stored data when the user explicitly deletes it. | UI button “Clear Selections”. | High |
| FR‑PERSIST‑006 | The application does not store any sensitive user data beyond seat selections. | Security compliance verified. | High |

### 2.5. Implied Functional Requirements (Generated)  

| ID | Title | Description | Priority |
|----|-------|-------------|----------|
| FR‑IMP‑001 | The application must not exceed the browser’s local storage limit of 5 MB. | Calculated via size check; error message shown if exceeded. | Medium |
| FR‑IMP‑002 | The application must persist selections across page reloads. | Verified by reloading and confirming unchanged UI. | High |
| FR‑IMP‑003 | The application must persist selections across browser restarts (if supported). | Tested by closing and reopening the browser. | Medium |
| FR‑IMP‑004 | The application must not store selections if the user is not logged in. | Session‑aware logic prevents storage on first visit. | High |
| FR‑IMP‑005 | The application must persist selections only for the current session if the browser’s storage quota is exceeded. | Fallback to temporary storage. | Medium |

---  

## 3. Non‑Functional Requirements  

| ID | Category | Requirement | Metric / Test | Priority |
|----|----------|-------------|---------------|----------|
| NFR‑SEC‑001 | Security | No PII is stored in local storage. | Static code analysis; no fields with PII. | Medium |
| NFR‑SEC‑002 | Security | The application validates user input (seat selection) to prevent injection. | Unit tests; input sanitization. | High |
| NFR‑PERF‑001 | Performance | Total cost calculation ≤ 200 ms. | Load test (10 concurrent users). | High |
| NFR‑PERF‑002 | Performance | UI rendering of seat map ≤ 100 ms. | End‑to‑end test; measure frame rate. | High |
| NFR‑AVL‑001 | Availability | Application reachable 99.9 % of the time. | 30‑day uptime monitoring. | High |
| NFR‑SCA‑001 | Scalability | 10 concurrent users → average response ≤ 30 ms. | Load test with 10 users. | Medium |
| NFR‑USU‑001 | Usability | Average clicks to complete selection ≤ 2. | Usability test (5 participants). | High |
| NFR‑MNT‑001 | Maintainability | Documentation ≥ 95 % completeness; code comments ≥ 80 %. | Code review checklist. | Medium |

---  

## 4. Technical Constraints & Integration  

| ID | Constraint | Description |
|----|------------|-------------|
| TF‑001 | Use of local storage is required for persistence. | Must implement `localStorage.setItem` / `getItem`. |
| TF‑002 | All calculations must be performed client‑side. | No server‑side cost aggregation; pure JavaScript arithmetic. |
| TF‑003 | The application must be fully responsive on browsers ≥ Chrome 110, Safari 16, Firefox 105. |
| TF‑004 | The seat map grid must be generated dynamically based on movie width/height. |
| TF‑005 | The application must handle the edge case where no seats are available. |
| TF‑006 | The application must gracefully degrade if local storage is disabled. |
| TF‑007 | The application must be unit‑tested with at least 80 % code coverage. |

---  

## 5. User Personas & Use Cases  

### 5.1. Personas  

| ID | Persona | Key Needs |
|----|---------|-----------|
| US‑001 | Customer (movie‑goer) | Browse movies, see seat availability, book seats instantly, retain selections across sessions. |
| US‑002 | Tech‑savvy user | Expect fast UI, no bugs, clear cost display, ability to cancel. |
| US‑003 | Casual user | Simple navigation, no learning curve, quick purchase. |

### 5.2. Use Case: Browse & Book Movie Seats  

| Step | Actor | Goal | Result |
|------|-------|------|--------|
| 1 | User | Open the application. | UI loads, list of movies displayed. |
| 2 | User | Select a movie. | Movie ID stored in UI and local storage. |
| 3 | User | Click seats until desired seats are chosen. | Seats marked “reserved”, timestamps recorded. |
| 4 | User | Verify total cost is displayed. | Cost panel updates to accurate amount. |
| 5 | User | Close the page or refresh. | Selections remain in local storage. |
| 6 | Later | Return to the application. | UI restores previous selections automatically. |
| 7 | User | Deletes selections. | UI and local storage cleared. |

### 5.3. Main Flows Table  

| Flow ID | Flow Name | Main Steps | Success Criteria |
|---------|-----------|------------|-------------------|
| UC‑001 | Browse & Select | 1‑5 above | All steps complete, cost ≤ 200 ms. |
| UC‑002 | Persist Across Reload | 5‑6 | Selections survive page refresh. |
| UC‑003 | Cancel Booking | 1‑4, 5 | UI resets, local storage cleared. |
| UC‑004 | Concurrent Conflict | 3‑4, 2‑3 | Seat cannot be selected twice; UI prevents. |
| UC‑005 | No Seats Available | 2‑3 | UI shows “Sold out”, cost = 0. |

---  

## 6. Edge Cases, Risks, and Gaps  

| ID | Edge Case | Impact | Risk | Mitigation |
|----|-----------|--------|------|------------|
| EC‑001 | User selects no seats → total cost = 0. | UI must display “No seats selected”. | Misleading UI. | FR‑CALC‑004 + UI rule. |
| EC‑002 | User closes browser → loses saved selections. | Persistence failure. | Loss of user data. | FR‑PERSIST‑002 + clear messaging. |
| EC‑003 | Seat selected by another user within 2 s → conflict. | Race condition. | Double‑booking. | FR‑SEAT‑005 + backend lock (if any). |
| EC‑004 | Browser storage quota exceeded. | Data loss. | Session loss. | TF‑006 fallback. |
| EC‑005 | Concurrent calculations exceed 200 ms. | Performance breach. | User frustration. | NFR‑PERF‑001 testing, load testing. |

### 6.1. Risks  

| ID | Risk | Probability | Severity | Owner |
|----|------|-------------|----------|-------|
| R‑001 | Storage quota exceeded → data loss. | Medium | High | Front‑end Engineer |
| R‑002 | Concurrent seat selection leads to double‑booking. | Low | High | Backend / UI Sync |
| R‑003 | UI does not update after cost calculation. | Medium | Medium | UI/UX |

### 6.2. Gaps  

- **Payment integration:** Not required now (out‑of‑scope).  
- **Offline support:** No requirement for offline mode; only local storage.  
- **Multi‑language support:** Not addressed; English only per assumption.  

---  

## 7. Assumptions  

| ID | Assumption |
|----|------------|
| A‑001 | The seat map data is supplied by a REST API that is always reachable within 500 ms. |
| A‑002 | The user has an active internet connection when the application is used. |
| A‑003 | The browser supports `localStorage` and modern CSS Grid for the seat map. |
| A‑004 | The movie list is static for the duration of the sprint. |
| A‑005 | No third‑party libraries will be used for the cost calculation. |
| A‑006 | The user will not attempt to manipulate localStorage keys. |
| A‑007 | The application will be deployed via HTTPS (TLS) for all API calls. |

---  

## 8. Traceability Matrix  

| FR/NFR ID | Feature / Non‑Functional Requirement | Verification Method |
|-----------|----------------------------------------|----------------------|
| FR‑UI‑001 | Users can browse a list of movies. | UI inspection, manual test. |
| FR‑UI‑002 | Users can select available seats. | Click test, seat map rendering. |
| FR‑CALC‑001 | Application calculates total cost. | Unit test with multiple seat combos. |
| FR‑PERSIST‑001 | Application stores user selections in local storage. | Inspect localStorage key, reload test. |
| NFR‑PERF‑001 | Total cost calculation ≤ 200 ms. | Load test (10 users). |
| NFR‑SEC‑001 | No PII stored. | Static analysis, code review. |
| NFR‑AVL‑001 | 99.9 % availability. | 30‑day monitoring report. |
| NFR‑SCA‑001 | 10 concurrent users → ≤ 30 ms. | Load test with 10 users. |
| NFR‑USU‑001 | ≤ 2 clicks to select seats. | Usability test. |
| TF‑001 | Use local storage for persistence. | Code verification. |
| TF‑002 | All calculations client‑side. | Code audit, no server code. |
| TC‑001 | No seat selected → cost 0. | Edge case test. |
| TC‑002 | Close browser → selections restored. | Functional test. |
| TC‑003 | Concurrent selection conflict prevented. | Concurrency test (2 users). |
| TC‑004 | No seats → UI shows “Sold out”. | Edge case test. |
| TC‑005 | Storage quota exceeded → fallback. | Simulate quota limit. |

---  

*End of Requirements Specification*