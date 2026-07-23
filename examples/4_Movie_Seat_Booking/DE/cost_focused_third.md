# DE.md

## 1. Executive Summary

This document outlines the design and estimation for a movie seat booking application, based on the approved Requirements Analysis (RA). The system enables users to select movies, choose seats, calculate costs, and persist selections using local storage. Key features include a responsive UI, dynamic cost calculation, and seat selection with overbooking prevention. The design prioritizes performance, security, and scalability within the constraints of a client-side architecture.

The system is structured as a **Single-Page Application (SPA)** using modern frontend technologies. All data is stored in **local storage**, eliminating the need for a backend server. The architecture ensures compliance with non-functional requirements such as 99.9% uptime, 1.5s load time, and WCAG 2.1 AA accessibility standards.

## 2. System Architecture

### Architecture Pattern Justification
- **Client-Side Only**: Due to technical constraint TC-001, the system relies entirely on local storage for data persistence, avoiding backend complexity.
- **SPA (Single-Page Application)**: Ensures fast interactions and a seamless user experience, aligning with performance requirements (NFR-PERF-001, NFR-PERF-002).
- **Modular Design**: Facilitates maintainability (NFR-MT-001) and scalability (NFR-SCAL-001).

### ASCII Diagram
```
+---------------------+
|   User Interface    |
| (React/Vue.js)      |
+----------+----------+
           |
           | User Actions
           v
+---------------------+
|   Seat Selection    |
| (Grid, Event Handlers)|
+----------+----------+
           |
           | Data Updates
           v
+---------------------+
|   Cost Calculation  |
| (Dynamic, Discounts) |
+----------+----------+
           |
           | Local Storage
           v
+---------------------+
|   Local Storage     |
| (localStorage API)  |
+---------------------+
```

### Component List
| Component | Description | RA Requirements Addressed |
|---------|-------------|---------------------------|
| Movie List | Displays available movies with showtimes | FR-MOV-001, FR-MOV-002 |
| Theater Layout | Visual seat grid with status indicators | FR-SEAT-001, FR-SEAT-003 |
| Cost Engine | Calculates totals, applies discounts | FR-COST-001, FR-COST-003 |
| Local Storage Manager | Handles persistence and retrieval | FR-LS-001, FR-LS-002 |
| UI/UX Layer | Responsive, accessible interface | NFR-USER-001, NFR-USER-002 |

## 3. Technology Stack

### Frontend
| Tool | Version | Justification |
|------|---------|---------------|
| React.js | 18.2.0 | Component-based architecture for dynamic UI updates (FR-SEAT-002, FR-COST-002). |
| TypeScript | 4.9.3 | Enforces type safety and modularity (NFR-MT-001). |
| Tailwind CSS | 3.3.3 | Rapid UI development with utility-first classes. |
| Axios | 1.6.2 | For potential future API integrations (not required here). |

### Backend
| Tool | Version | Justification |
|------|---------|---------------|
| None | N/A | Technical constraint TC-001 mandates no backend. |

### Database
| Component | Type | Justification |
|---------|------|---------------|
| Local Storage | JSON | Client-side persistence (FR-LS-001, FR-LS-002). |
| Session Storage | N/A | Not used due to RA's focus on cross-session persistence. |

## 4. Infrastructure & Deployment

### Cloud Services
- **Hosting**: Static site hosting (Netlify/Vercel) for SPA deployment.
- **CI/CD**: GitHub Actions for automated testing and deployment pipelines.

### Environments
| Environment | Purpose | Configuration |
|-------------|---------|---------------|
| Development | Local testing | No minification, live reload. |
| Staging | Pre-production testing | Mirrors production. |
| Production | Live deployment | Optimized assets, security headers. |

### Infrastructure Diagram
```
[User Browser] --> [Netlify/Vercel] --> [Local Storage]
```

## 5. Data Flows for Key Use Cases

### Use Case: Book Movie Seats
1. **Step 1**: User selects a movie (FR-MOV-003) → Movie data is fetched from local storage or hardcoded list.
2. **Step 2**: Theater layout is rendered (FR-SEAT-001) → Seat status (available/booked) is retrieved from local storage.
3. **Step 3**: User selects/deselects seats (FR-SEAT-002, FR-SEAT-005) → Seat state is updated in local storage.
4. **Step 4**: Cost is recalculated (FR-COST-002) → Total is updated in real-time.
5. **Step 5**: Data is saved to local storage (FR-LS-001) → `localStorage.setItem('seatSelections', JSON.stringify(data))`.

### Use Case: Clear Saved Data
1. **Step 1**: User navigates to settings → UI triggers a confirmation modal.
2. **Step 2**: User confirms clear → `localStorage.clear()` is executed.
3. **Step 3**: UI resets to default state (FR-LS-002).

## 6. Database Schema Overview

### Local Storage Structure
| Key | Value Type | Description | RA Requirements Addressed |
|-----|------------|-------------|---------------------------|
| `selectedMovie` | Object | Movie details (title, showtime, theater) | FR-MOV-001, FR-MOV-003 |
| `selectedSeats` | Array | Seat IDs and status (selected/booked) | FR-SEAT-001, FR-SEAT-003 |
| `userPreferences` | Object | User-specific settings (e.g., language) | NFR-USER-001 |
| `lastVisited` | Date | Timestamp for session tracking | NFR-SCAL-001 |

### Example JSON
```json
{
  "selectedMovie": {
    "title": "The Matrix",
    "showtime": "18:00",
    "theater": "Cinema 1"
  },
  "selectedSeats": ["A1", "A2", "B3"],
  "userPreferences": {
    "language": "en"
  }
}
```

## 7. API Surface Overview

### Frontend APIs
| API | Method | Description | RA Requirements Addressed |
|-----|--------|-------------|---------------------------|
| `fetchMovies()` | GET | Retrieves movie list from local storage or hardcoded data | FR-MOV-001 |
| `selectSeat(seatId)` | POST | Updates seat status in local storage | FR-SEAT-002 |
| `calculateCost(seats)` | GET | Returns total cost with discounts | FR-COST-002, FR-COST-003 |
| `saveToLocalStorage(data)` | POST | Persists data to `localStorage` | FR-LS-001 |

### Local Storage API
| Method | Description | RA Requirements Addressed |
|--------|-------------|---------------------------|
| `setItem(key, value)` | Saves data to local storage | FR-LS-001 |
| `getItem(key)` | Retrieves data from local storage | FR-LS-002 |
| `clear()` | Clears all stored data | FR-LS-003 |

## 8. Development Effort Estimation

### Module T-Shirt Sizing
| Module | Size | Effort (Person-Days) | Justification |
|--------|------|----------------------|---------------|
| Movie Selection | L | 10 | Requires dynamic filtering and rendering (FR-MOV-002). |
| Seat Selection | XL | 15 | Complex UI interactions and overbooking logic (FR-SEAT-003). |
| Cost Calculation | M | 7 | Simple math with conditional discounts (FR-COST-003). |
| Local Storage | M | 5 | Basic CRUD operations (FR-LS-001, FR-LS-002). |
| UI/UX | L | 10 | Accessibility and responsiveness (NFR-USER-002). |

### Phased Delivery
- **Phase 1 (4 weeks)**: Core features (movie selection, seat selection, cost calculation).
- **Phase 2 (2 weeks)**: Local storage integration and edge case handling (EC-001, EC-002).
- **Phase 3 (2 weeks)**: Testing, optimization, and documentation.

### Team Composition
| Role | Number | Responsibilities |
|------|--------|------------------|
| Frontend Developer | 2 | Implement UI, APIs, and local storage. |
| UX Designer | 1 | Ensure accessibility and responsiveness. |
| QA Engineer | 1 | Test edge cases and performance. |

## 9. Technical Risks & Mitigation

| Risk | Mitigation Strategy | RA Requirement Addressed |
|------|----------------------|--------------------------|
| Data Loss (RISK-001) | Auto-save on every interaction; confirmation prompts for navigation | NFR-SEC-001 |
| Browser Incompatibility (RISK-002) | Test on Chrome, Firefox, Safari, Edge (TC-002) | TC-002 |
| Local Storage Full (EC-002) | Notify user and prevent saving if storage is full | FR-LS-001 |
| Overbooking (EC-001) | Disable booked seats and show warnings | FR-SEAT-003 |

## 10. Security Architecture

### Data Integrity
- **Local Storage**: No server-side validation, but input sanitization is applied to prevent injection attacks (NFR-SEC-002).
- **Client-Side Validation**: Ensures seat selections and movie data are within defined constraints (e.g., max 6 seats).

### Access Control
- **No Authentication**: Since the system is client-side only, no user authentication is implemented. Data is isolated per browser session.

### Threat Model
| Threat | Mitigation |
|--------|------------|
| Tampering with Local Storage | Use JSON structure and validation to detect corruption. |
| XSS (Cross-Site Scripting) | Sanitize all user inputs and avoid inline scripts. |

## 11. Monitoring & Observability

### Tools
- **Error Tracking**: Sentry for client-side error logging.
- **Performance Monitoring**: Lighthouse for audits (NFR-PERF-001).
- **User Feedback**: In-app surveys for usability testing (NFR-USER-001).

### Metrics
| Metric | Target | Monitoring Tool |
|--------|--------|-----------------|
| Page Load Time | <1.5s | Lighthouse |
| Seat Selection Latency | <300ms | Performance API |
| Uptime | 99.9% | Netlify/Vercel uptime dashboard |

## 12. Cost Estimation

### Monthly Breakdown (USD)
| Category | Cost | Notes |
|----------|------|-------|
| Developer Salaries | $12,000 | 2 frontend devs, 1 QA engineer (assuming $50/hour). |
| Hosting (Netlify) | $10 | Basic plan for 100k page views/month. |
| CI/CD Tools | $0 | GitHub Actions is free. |
| Monitoring Tools | $20 | Sentry and Lighthouse integration. |
| **Total** | **$12,030** | |

## 13. Recommendations & Next Steps

1. **Adopt React with TypeScript** for scalable, maintainable code.
2. **Implement Accessibility Testing** using axe-core or Lighthouse.
3. **Conduct Load Testing** to validate scalability (NFR-SCAL-001).
4. **Document APIs** for future maintenance (NFR-MT-002).
5. **Add Fallback for Storage Full** (EC-002) with user guidance.

## 14. Traceability Matrix

| Requirement ID | Source | Test Case ID | Status |
|----------------|--------|--------------|--------|
| FR-MOV-001 | RA 2.1 | TC-MOV-001 | Implemented |
| FR-SEAT-001 | RA 2.2 | TC-SEAT-001 | Implemented |
| NFR-PERF-001 | RA 3.2 | TC-PERF-001 | Implemented |
| TC-001 | RA 4.1 | TC-TC-001 | Implemented |
| EC-001 | RA 6.1 | TC-EC-001 | Implemented |
| RISK-001 | RA 6.2 | TC-RISK-001 | Implemented |