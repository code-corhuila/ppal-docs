# ADR-001 — State Management Pattern

- **ID:** ADR-001
- **Date:** 2026-08-28
- **Last Updated:** 2026-10-25 (stack-specific refinement)
- **Status:** Accepted
- **Authors:** Gustavo Gallego

---

## Context

PantryPal is planned as an Ionic + Angular mobile app with a small, single-pantry MVP. The product list and form must represent API loading, success, empty data, validation, saving, and failure without mixing business rules into page components. This is an individual academic project with a limited schedule, so a heavyweight state-management library would add setup and learning effort without clear MVP value.

**Known constraints:**
- A single mobile client and a single REST API; the API is the source of truth.
- No accounts, authentication, offline cache, or offline write queue in the MVP.
- State transitions must be testable independently of rendered Ionic UI.
- The intended stack is Ionic + Angular + Capacitor. This is an architectural decision, not a claim that product features have already been implemented.

---

## Decision

**We decided:** use an MVVM-style feature facade/view-model with one immutable state snapshot per screen, exposed through Angular read-only Signals; use Angular Router and Ionic components for presentation, and keep remote HTTP request handling in the data layer.

Implementation rules:
- A feature facade owns user actions and state transitions; the page/component renders state and forwards user intents.
- Expose state as a read-only signal and replace state objects on transitions; do not mutate arrays or nested objects in place.
- Use computed signals for derived presentation values where useful. Keep expiration-date classification and validation rules in domain functions/use cases, not in templates.
- Use Angular HttpClient (and RxJS for request/response streams where appropriate) behind a repository/data-source boundary. RxJS is allowed for asynchronous I/O; do not introduce a second global UI-state pattern.
- Represent loading, loaded/empty, validation, saving, and operation errors explicitly. API/network failures must not be represented as an empty list or a successful mutation.
- Do not add NgRx or another global state library unless a later ADR justifies it.

**Justification:** this keeps the state flow unidirectional, testable, and close to the existing Angular stack while avoiding unnecessary dependencies for a focused individual project.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|-------------|------|------|-----------------------|
| Feature facade / MVVM with immutable state in Angular Signals (chosen) | Small API surface, explicit state transitions, derived state support, simple unit tests | Requires discipline around immutable updates | Best fit for the small Ionic + Angular MVP |
| NgRx global store | Mature ecosystem, powerful tooling for complex applications | Extra concepts, boilerplate, and dependencies for one small feature | Disproportionate to the current scope |
| MVI with explicit actions and reducers | Highly explicit and predictable transitions | More ceremony for simple CRUD screens | Reconsider only if state complexity grows |
| Mutable state directly in page components | Fast for a first mockup | Couples rendering, networking, validation, and business logic; harder to test | Rejected to preserve layer boundaries and testability |

---

## Consequences

**Positive:**
- Each screen's important states and transitions can be unit-tested without rendering Ionic components.
- Page components remain focused on layout, accessibility, and forwarding user actions.
- There is one agreed state-management approach across PantryPal features.

**Negative / Trade-offs:**
- A small amount of state/facade boilerplate is required.
- Deeply nested state must be replaced immutably rather than mutated in place.

**Impact on the system:**
- Affected features/screens: pantry list, add/edit product form, delete confirmation feedback, and all loading/empty/error states.
- Documents to keep aligned: layers-and-state.md, screen specifications, and testing-strategy.md.

## References

- Angular Signals documentation: https://angular.dev/guide/signals
- Angular HTTP client documentation: https://angular.dev/guide/http
- Related to ADR-002 — MVP Scope and ADR-003 — Navigation Model.
