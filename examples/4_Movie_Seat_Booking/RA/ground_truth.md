# Software Requirements Specification — Movie Seat Booking Application

| Field            | Detail                                      |
|------------------|---------------------------------------------|
| **Document ID**  | SRS-MSB-2026-04                             |
| **Version**      | 1.0                                         |
| **Date**         | 2026-04-27                                  |
| **Author**       | Requirements Analysis Team                  |
| **Status**       | Draft                                       |

---

## 1. Introduction

### 1.1 Purpose

This document specifies the functional, non-functional, and technical requirements for the **Movie Seat Booking** web application. It serves as the authoritative reference for design, implementation, testing, and acceptance of the system.

### 1.2 Scope

The application enables users to browse a catalog of movies, view a theater seat map, select available seats, see a real-time price total, and have their selections persisted in the browser via local storage. The system is a client-side single-page application with no back-end authentication or payment gateway in the initial release.

### 1.3 Definitions & Acronyms

| Term               | Definition                                                                 |
|--------------------|---------------------------------------------------------------------------|
| **SPA**            | Single-Page Application                                                   |
| **Local Storage**  | Browser Web Storage API that persists key-value data across sessions      |
| **Seat Map**       | Visual grid representing theater rows and columns                         |
| **Occupied Seat**  | A seat already booked and unavailable for selection                       |
| **Selected Seat**  | A seat the current user has chosen but not yet confirmed                  |
| **Available Seat** | A seat open for selection                                                 |
| **Session**        | A single continuous period of user interaction with the application       |
| **Ticket Price**   | The per-seat cost associated with a specific movie                        |

### 1.4 Intended Audience

- Front-end developers and UI/UX designers
- QA engineers responsible for test-plan creation
- Project managers tracking scope and delivery
- Stakeholders reviewing feature completeness

---

## 2. Functional Requirements

### 2.1 Movie Selection (FR-MOV)

| ID          | Title                        | Description                                                                                                   | Priority |
|-------------|------------------------------|---------------------------------------------------------------------------------------------------------------|----------|
| FR-MOV-001  | Display Movie List           | The system shall render a dropdown or equivalent selector populated with at least three movie titles.          | High     |
| FR-MOV-002  | Show Ticket Price            | Upon selecting a movie, the system shall display the per-seat ticket price next to or below the selector.      | High     |
| FR-MOV-003  | Dynamic Price Association    | Each movie in the catalog shall be associated with a unique ticket price stored in the application data model. | High     |
| FR-MOV-004  | Default Movie Selection      | On initial load (no stored state), the system shall pre-select the first movie in the catalog.                 | Medium   |
| FR-MOV-005  | Restore Last Selected Movie  | If a previous session exists in local storage, the system shall restore the last selected movie on page load.  | High     |
| FR-MOV-006  | Movie Change Reset Prompt    | When the user switches movies, the system shall clear previously selected seats and update the seat map.       | High     |

### 2.2 Seat Map Display (FR-SEAT)

| ID           | Title                        | Description                                                                                                          | Priority |
|--------------|------------------------------|----------------------------------------------------------------------------------------------------------------------|----------|
| FR-SEAT-001  | Render Seat Grid             | The system shall render a grid of seats organized by rows and columns representing the theater layout.                | High     |
| FR-SEAT-002  | Visual State — Available     | Available seats shall be rendered in a neutral color (e.g., dark gray) distinguishable from other states.             | High     |
| FR-SEAT-003  | Visual State — Selected      | User-selected seats shall be highlighted in an accent color (e.g., blue) immediately upon click.                      | High     |
| FR-SEAT-004  | Visual State — Occupied      | Occupied seats shall be rendered in a distinct muted color (e.g., light gray/white) and shall not respond to clicks.  | High     |
| FR-SEAT-005  | Seat Legend                  | A legend shall be displayed explaining the color coding for available, selected, and occupied seat states.            | Medium   |
| FR-SEAT-006  | Screen Indicator             | A visual "screen" indicator shall appear at the top of the seat grid to orient the user.                              | Low      |
| FR-SEAT-007  | Seat Grid Dimensions         | The default theater layout shall contain a minimum of 6 rows and 8 columns (48 seats).                               | Medium   |
| FR-SEAT-008  | Responsive Seat Sizing       | Seat elements shall scale appropriately on viewports ranging from 320 px to 1920 px wide.                             | Medium   |

### 2.3 Seat Selection & Deselection (FR-SEL)

| ID          | Title                          | Description                                                                                                       | Priority |
|-------------|--------------------------------|-------------------------------------------------------------------------------------------------------------------|----------|
| FR-SEL-001  | Select Available Seat          | Clicking an available seat shall toggle its state to "selected" and update the total cost.                         | High     |
| FR-SEL-002  | Deselect Selected Seat         | Clicking a selected seat shall toggle its state back to "available" and decrease the total cost.                   | High     |
| FR-SEL-003  | Block Occupied Seat Click      | Clicking an occupied seat shall produce no state change and no visual feedback beyond the cursor style.            | High     |
| FR-SEL-004  | Real-Time Seat Count           | The UI shall display the current number of selected seats, updated on every selection or deselection event.        | High     |
| FR-SEL-005  | Maximum Seat Limit             | The system shall enforce a configurable maximum number of selectable seats per session (default: 10).              | Medium   |
| FR-SEL-006  | Limit Exceeded Notification    | When the user attempts to exceed the seat limit, a non-blocking notification shall inform them of the constraint.  | Medium   |
| FR-SEL-007  | Keyboard Accessibility         | Users shall be able to navigate and toggle seats using keyboard Tab and Enter keys.                                | Medium   |

### 2.4 Pricing & Cost Summary (FR-PRC)

| ID          | Title                          | Description                                                                                                          | Priority |
|-------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------|----------|
| FR-PRC-001  | Calculate Total Cost           | The system shall compute total cost as `selectedSeatCount × ticketPrice` and display it in real time.                 | High     |
| FR-PRC-002  | Currency Format                | The total cost shall be displayed with a currency symbol (default: `$`) and two decimal places.                       | Medium   |
| FR-PRC-003  | Zero-State Display             | When no seats are selected the summary shall show `$0.00` and `0 seats selected`.                                    | High     |
| FR-PRC-004  | Price Update on Movie Change   | Switching the selected movie shall recalculate the total cost using the new movie's ticket price.                     | High     |

### 2.5 Data Persistence — Local Storage (FR-PER)

| ID          | Title                              | Description                                                                                                                  | Priority |
|-------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------|----------|
| FR-PER-001  | Persist Selected Movie             | The system shall write the selected movie index to local storage on every movie change event.                                 | High     |
| FR-PER-002  | Persist Selected Seats             | The system shall write an array of selected seat indices to local storage on every seat toggle event.                         | High     |
| FR-PER-003  | Restore State on Load              | On page load the system shall read local storage and restore the movie selection and seat selections.                         | High     |
| FR-PER-004  | Handle Missing/Corrupt Storage     | If local storage data is missing, corrupt, or schema-incompatible, the system shall fall back to default state without error. | High     |
| FR-PER-005  | Storage Key Namespacing            | All local storage keys shall be prefixed with `msb_` to avoid collisions with other applications on the same origin.          | Medium   |
| FR-PER-006  | Clear Storage Action               | The system should provide a "Reset" button allowing users to clear all persisted data and return to default state.            | Low      |

---

## 3. Non-Functional Requirements

### 3.1 Performance (NFR-PERF)

| ID            | Metric                       | Target                                                        | Priority |
|---------------|------------------------------|---------------------------------------------------------------|----------|
| NFR-PERF-001  | Initial Page Load            | First Contentful Paint ≤ 1.5 s on a 4G connection             | High     |
| NFR-PERF-002  | Seat Toggle Latency          | Visual state change within ≤ 50 ms of click event             | High     |
| NFR-PERF-003  | Local Storage I/O            | Read/write operations shall complete in ≤ 10 ms               | Medium   |
| NFR-PERF-004  | Bundle Size                  | Total transferred JavaScript + CSS ≤ 150 KB gzipped           | Medium   |

### 3.2 Usability (NFR-USA)

| ID           | Metric                        | Target                                                                                   | Priority |
|--------------|-------------------------------|------------------------------------------------------------------------------------------|----------|
| NFR-USA-001  | Learnability                  | A first-time user shall complete a booking flow within 30 seconds without instruction.    | High     |
| NFR-USA-002  | Color Contrast                | All seat states shall meet WCAG 2.1 AA contrast ratio (≥ 4.5:1 for text, ≥ 3:1 for UI). | Medium   |
| NFR-USA-003  | Mobile Touch Targets          | Interactive seat elements shall have a minimum touch target of 44 × 44 px.                | Medium   |
| NFR-USA-004  | Error Prevention              | Occupied seats shall use `cursor: not-allowed` to signal non-interactivity.               | Medium   |

### 3.3 Availability & Reliability (NFR-AVL)

| ID           | Metric                        | Target                                                                                     | Priority |
|--------------|-------------------------------|--------------------------------------------------------------------------------------------|----------|
| NFR-AVL-001  | Offline Functionality         | The application shall remain fully functional without a network connection after first load. | High     |
| NFR-AVL-002  | Browser Compatibility         | The application shall function correctly on the latest two major versions of Chrome, Firefox, Safari, and Edge. | High |
| NFR-AVL-003  | Graceful Degradation          | If local storage is unavailable (private mode), the app shall still operate within the session without persistence. | Medium |

### 3.4 Security (NFR-SEC)

| ID           | Metric                        | Target                                                                                 | Priority |
|--------------|-------------------------------|----------------------------------------------------------------------------------------|----------|
| NFR-SEC-001  | XSS Prevention                | All dynamic content rendered in the DOM shall be sanitized to prevent script injection. | High     |
| NFR-SEC-002  | Storage Integrity             | Data read from local storage shall be validated against an expected schema before use.  | Medium   |

### 3.5 Scalability & Maintainability (NFR-SCA)

| ID           | Metric                        | Target                                                                                         | Priority |
|--------------|-------------------------------|------------------------------------------------------------------------------------------------|----------|
| NFR-SCA-001  | Configurable Theater Layout   | Row and column counts shall be defined via configuration, not hard-coded in rendering logic.    | Medium   |
| NFR-SCA-002  | Movie Catalog Extensibility   | Adding a new movie shall require changes to a single data structure (array/object) only.        | Medium   |
| NFR-SCA-003  | Code Modularity               | The codebase shall separate concerns: data model, rendering, event handling, and persistence.   | Medium   |

---

## 4. Technical Constraints & Integration

| ID         | Constraint                                                                                                     |
|------------|----------------------------------------------------------------------------------------------------------------|
| TC-001     | The application shall be implemented as a client-side SPA using HTML, CSS, and vanilla JavaScript (or a lightweight framework). |
| TC-002     | No server-side component or database is required; all data persistence uses the Web Storage API (`localStorage`). |
| TC-003     | The application shall not require user authentication or account creation.                                       |
| TC-004     | No third-party payment gateway integration is in scope for the initial release.                                  |
| TC-005     | The deployment artifact shall consist of static files servable from any HTTP server or CDN.                      |

---

## 5. User Personas & Use Cases

### 5.1 Personas

| Persona          | Description                                                                                       |
|------------------|---------------------------------------------------------------------------------------------------|
| **Casual Viewer**| A non-technical user who wants to quickly pick a movie, choose seats, and see the price.           |
| **Group Planner**| A user booking multiple seats for friends/family who needs clear visual feedback on group selection.|
| **Returning User**| A user who previously selected seats, closed the browser, and expects to resume where they left off.|

### 5.2 Use Cases

| UC-ID  | Title                    | Actor          | Precondition                 | Main Flow                                                                                                                                              | Postcondition                              |
|--------|--------------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------|
| UC-001 | Select Movie & Seats     | Casual Viewer  | Application loaded           | 1. User selects a movie from the dropdown. 2. Seat map updates. 3. User clicks available seats. 4. Cost summary updates in real time.                  | Selected seats highlighted; total displayed.|
| UC-002 | Deselect a Seat          | Casual Viewer  | At least one seat selected   | 1. User clicks a selected seat. 2. Seat returns to available state. 3. Count and cost decrease.                                                        | Seat reverts; totals recalculated.          |
| UC-003 | Resume Previous Session  | Returning User | Local storage contains state | 1. User opens the application. 2. System reads local storage. 3. Movie and seats restored automatically.                                               | UI matches the last saved state.            |
| UC-004 | Switch Movie Mid-Session | Group Planner  | Seats already selected       | 1. User changes movie in dropdown. 2. Previous seats are cleared. 3. New price is shown. 4. User selects new seats.                                    | New movie active; old selections cleared.   |
| UC-005 | Attempt Occupied Seat    | Casual Viewer  | Occupied seats exist on map  | 1. User clicks an occupied seat. 2. No state change occurs. 3. Cursor indicates seat is unavailable.                                                   | No change to selection or cost.             |

---

## 6. Edge Cases, Risks & Gaps

### 6.1 Edge Cases

| EC-ID   | Scenario                                      | Expected Behavior                                                            |
|---------|-----------------------------------------------|-----------------------------------------------------------------------------|
| EC-001  | User clears browser local storage externally   | Application loads with default state; no errors thrown.                      |
| EC-002  | Local storage quota exceeded                   | Write failure is caught; application continues without persistence.          |
| EC-003  | All seats are occupied                         | No seats are clickable; cost shows $0.00; informational message displayed.   |
| EC-004  | Browser does not support local storage         | Application functions within the session; persistence silently disabled.     |
| EC-005  | User rapidly toggles the same seat             | Each toggle is processed sequentially; no duplicate state entries created.   |
| EC-006  | Movie catalog is empty or fails to load        | A user-friendly fallback message is shown instead of a blank dropdown.       |

### 6.2 Risks

| Risk-ID | Description                                              | Likelihood | Impact | Mitigation                                          |
|---------|----------------------------------------------------------|------------|--------|-----------------------------------------------------|
| RSK-001 | Local storage data tampered with by user via DevTools     | Medium     | Low    | Validate schema on read; fall back to defaults.      |
| RSK-002 | Seat grid not usable on very small screens (< 320 px)    | Low        | Medium | Implement horizontal scroll or zoom for seat map.    |
| RSK-003 | Future requirement for real booking needs back-end        | High       | High   | Design data model to be back-end-ready.              |

### 6.3 Identified Gaps

- No confirmation or checkout step exists; the current scope covers selection only.
- No accessibility audit has been performed; WCAG compliance targets are stated but untested.
- Multi-language / localization support is not addressed in this release.

---

## 7. Assumptions

| ID      | Assumption                                                                                          |
|---------|-----------------------------------------------------------------------------------------------------|
| ASM-001 | The movie catalog (titles and prices) is static and hard-coded in the application source.            |
| ASM-002 | Occupied seats are pre-defined in the source data; there is no real-time availability feed.          |
| ASM-003 | Only one theater/screen layout exists; multi-screen support is out of scope.                         |
| ASM-004 | The application targets modern evergreen browsers; IE 11 support is not required.                    |
| ASM-005 | Currency and locale are fixed to USD (`$`) for the initial release.                                  |
| ASM-006 | No user authentication, registration, or profile management is required.                             |

---

## 8. Traceability Matrix

| Requirement ID | Use Case(s)          | Persona(s)                     | Test Approach              |
|----------------|----------------------|--------------------------------|----------------------------|
| FR-MOV-001     | UC-001               | Casual Viewer, Group Planner   | UI rendering test          |
| FR-MOV-002     | UC-001               | Casual Viewer                  | Unit test on price display |
| FR-MOV-005     | UC-003               | Returning User                 | Integration test (storage) |
| FR-MOV-006     | UC-004               | Group Planner                  | Functional UI test         |
| FR-SEAT-001    | UC-001, UC-005       | All                            | Visual regression test     |
| FR-SEAT-004    | UC-005               | Casual Viewer                  | Click-event assertion      |
| FR-SEL-001     | UC-001               | Casual Viewer, Group Planner   | Click + state assertion    |
| FR-SEL-002     | UC-002               | Casual Viewer                  | Toggle state test          |
| FR-SEL-005     | UC-001               | Group Planner                  | Boundary value test        |
| FR-PRC-001     | UC-001, UC-002       | All                            | Calculation unit test      |
| FR-PRC-004     | UC-004               | Group Planner                  | Recalculation assertion    |
| FR-PER-001     | UC-003               | Returning User                 | Storage read/write test    |
| FR-PER-003     | UC-003               | Returning User                 | Page reload integration    |
| FR-PER-004     | EC-001, EC-004       | Returning User                 | Negative / fault test      |
| NFR-PERF-001   | —                    | All                            | Lighthouse audit           |
| NFR-USA-002    | —                    | All                            | Automated contrast check   |
| NFR-AVL-001    | —                    | Returning User                 | Offline simulation test    |
| NFR-SEC-001    | —                    | All                            | Static analysis / CSP test |

---

*End of Document — SRS-MSB-2026-04 v1.0*
