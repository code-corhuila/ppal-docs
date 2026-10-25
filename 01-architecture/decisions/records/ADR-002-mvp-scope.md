# ADR-002 — Minimum Viable Product Scope

- **ID:** ADR-002
- **Date:** 2026-10-25
- **Status:** Accepted
- **Authors:** Gustavo Gallego

---

## Context

PantryPal is an individual academic project intended to deliver a focused mobile pantry-inventory experience. The first design proposed a broader set of capabilities, while the Activity 1 requirements emphasize a clear problem, a justified initial architecture, a coherent prototype, a prioritized backlog, and initial environment validation. Implementing too many peripheral capabilities in the first increment would increase design and delivery risk and make acceptance criteria harder to verify.

The instructor authorized an individually developed project and advised recording the MVP scope explicitly in this repository. The MVP must describe the behavior that the project will actually implement; optional ideas must not be presented as committed functionality.

**Known constraints:**
- One developer and a limited academic project schedule.
- One mobile application, one REST API, one PostgreSQL database, and one infrastructure configuration; the repositories are separate, not a monorepo.
- The current Ionic/Angular repository is an initial scaffold. PantryPal product functionality has not yet been implemented.
- The API and database are part of the proposed architecture, but the current MVP has no accounts or authentication.

---

## Decision

**We decided:** define PantryPal's MVP as a single demonstration pantry focused on product inventory and expiration handling, with no user accounts or login.

### Included in the MVP — Must priority

The Must-have backlog consists of eight stories:

- **US-01 — View all pantry products:** load and display products from the REST API and distinguish a successful empty response from a connection failure.
- **US-02 — Add a product:** create a product after validating its fields and show success only after the API confirms the operation.
- **US-03 — Edit a product:** edit an existing record, including setting, changing, or clearing the optional expiration date.
- **US-04 — Delete a product:** ask for confirmation and remove it from the list only after the API confirms deletion.
- **US-07 — Calculate expiration status:** classify products as Expired, Expiring Soon, Not Expiring Soon, or No Expiration Date using the agreed calendar-date rules.
- **US-08 — Sort by expiration date:** apply ascending date order, with products without an expiration date last.
- **US-09 — Validate product data:** require a nonblank name, quantity greater than zero, allowed category and unit, appropriate integer/decimal rules, and a valid optional date. The API independently validates incoming data.
- **US-10 — Handle API failures:** provide clear connection/operation errors and retry behavior; do not report success before server confirmation.

### Lower-priority stories

- **Should — US-05 — Search by product name:** useful convenience, but not required for the core inventory flow.
- **Should — US-11 — Display the empty pantry state:** show an empty-state message and an Add Product action only when the API successfully returns no products. US-01 must still distinguish empty data from failure.
- **Should — US-12 — Display no-results feedback:** explain when search criteria return no products; this depends on search or other criteria being available.
- **Could — US-06 — Filter by expiration status:** add filters for All Products, Expiring Soon, Expired, and No Expiration Date only if all Must stories pass and capacity remains.

The priority definitions, rationale, and recommended delivery order are recorded in [Product Backlog Priorities](../../../00-governance/product-backlog-priorities.md). The twelve detailed story issues are now present in the GitHub Project `ppal-backlog` (confirmed by the user's screenshot on 2026-10-25). Their titles encode the priority, and the user has created a single-select project field named `Prioridad`. The user reports that the `Prioridad` field offers Critical, High, Medium, and Low. For this board, map Critical to Must, High to Should, Medium to Could, and leave Low unused; this is a display mapping only and does not change the MoSCoW scope.

The MVP has one demonstration pantry. Product records are independent, so repeated names are allowed. Product fields are `id`, `name`, `quantity`, `unit`, optional `expirationDate`, `category`, `createdAt`, and `updatedAt`. The API is the source of truth; the mobile app does not connect directly to PostgreSQL.

### Not included in the MVP

- Account registration, login, logout, and authentication features (including advanced authentication).
- Multiple user accounts, multiple pantries, household sharing, and account-specific inventory.
- Camera access, product photos, local notifications, push notifications, and automatic reminders.
- Offline cache and offline operation. In particular, no offline write queue or synchronization is promised.
- Shopping lists, recipes, consumption history, analytics/reports, barcode scanning, and automatic unit conversion.
- Separate microservices; the API remains a single modular service.

These exclusions keep the initial delivery small and testable. They may be reconsidered through a new, explicit scope decision rather than silently added to the MVP.

---

## Justification

The chosen scope concentrates on the problem's central value: recording pantry products and making expiration information reliable and visible. CRUD, validation, expiration rules, ordering, and API-failure handling form a complete vertical slice across the mobile client, API, and database. The eight Must stories have observable acceptance criteria and must be verified with suitable unit, API-integration, and UI/end-to-end tests.

Search, filtering, the empty-pantry call-to-action, and no-results feedback improve convenience but are not all release blockers. They are differentiated as Should or Could so the first increment remains feasible; US-06 is explicitly optional. Priorities should be reviewed after all Must stories meet the Definition of Done, not treated as a promise to implement every story.

Authentication, camera, and notifications add security, platform-permission, data-lifecycle, and testing requirements that are disproportionate to the initial demonstration scope.

Because this MVP has no authentication, the API must be used only in a controlled development or demonstration environment. It must not be exposed as a public multi-user production service or store sensitive personal data.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|-------------|------|------|-----------------------|
| Focused single-pantry inventory (chosen) | Small, traceable, testable vertical slice; matches the platform's Activity 1 scope | No personal accounts or native reminder/photo features | Chosen to keep the MVP feasible and its promises verifiable |
| Full household platform with authentication, offline cache, camera, and notifications | Broader feature set and more complete household use cases | Larger scope; more security, permission, persistence, and platform-testing work | Deferred to avoid overloading the initial MVP |
| UI-only prototype with no API/database contract | Fastest initial mockup | Does not establish end-to-end data boundaries or persistence expectations | Discarded because the proposed product architecture includes one REST API and one PostgreSQL database |

---

## Consequences

**Positive:**
- Scope and backlog priorities are explicit and traceable.
- The first implementation can be verified end to end through the mobile app, API, and database.
- The prototype can be refined around a stable core flow without implying that static interactions persist data.

**Negative / Trade-offs:**
- The MVP does not provide user-specific data isolation; it is suitable only for a controlled demonstration environment.
- Search and a friendly empty/no-results experience are lower priority; expiration-status filtering is optional.
- Users will not receive native reminders, take product photos, or use the pantry offline.

**Impact on the system:**
- Affected features/screens: product list, add/edit form, delete confirmation, expiration-status presentation, loading/error states, and optional empty/search/no-results states.
- Affected repositories: `ppal-docs`, `ppal-app`, `ppal-api`, `ppal-db`, and `ppal-infra`.
- Documents covered by this decision set: product backlog priority register, architecture/state and navigation guidance, product domain model, API contract, prototype state specification, and MVP acceptance test matrix. The GitHub Project board and actual Figma frames/screenshots still require synchronization.
- Related ADRs: ADR-001 — State management pattern and ADR-003 — Navigation model.

## References

- [Activity 1 requirements published in the course platform].
- Instructor feedback and scope guidance for PantryPal, October 2026.
- Related to: [ADR-001 — State management pattern](./ADR-001-state-management-pattern.md).
