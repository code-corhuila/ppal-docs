# ADR-002 — Minimum Viable Product Scope

- **ID:** ADR-002
- **Date:** 2026-10-25
- **Status:** Accepted
- **Authors:** Gustavo Gallego

### Implementation status note — 2026-10-30

This note records evidence gathered after the scope decision; it does not change the accepted scope. On the review branches, the app has passed 22 local unit tests, lint, and web builds; the API has passed 9 automated tests and a TypeScript build. The local Compose stack reached healthy PostgreSQL and API states, and manual Android-emulator tests verified list loading plus create, update, and delete against the API/database. GitHub Actions jobs are currently blocked before their steps start because GitHub reports the account is locked due to a billing issue. Those jobs are neither passing nor evidence of source-code test failures. No public cloud deployment, signed release, or store publication is claimed.

---

## Context

PantryPal is an individual academic project intended to deliver a focused mobile pantry-inventory experience. The first design proposed a broader set of capabilities, while the Activity 1 requirements emphasize a clear problem, a justified initial architecture, a coherent prototype, a prioritized backlog, and initial environment validation. Implementing too many peripheral capabilities in the first increment would increase design and delivery risk and make acceptance criteria harder to verify.

The instructor authorized an individually developed project and advised recording the MVP scope explicitly in this repository. The MVP must describe the behavior that the project will actually implement; optional ideas must not be presented as committed functionality.

**Known constraints:**
- One developer and a limited academic project schedule.
- One mobile application, one REST API, one PostgreSQL database, and one infrastructure configuration; the repositories are separate, not a monorepo.
- At the time this decision was recorded, the Ionic/Angular repository was an initial scaffold. The implementation has since been added on a review branch; the historical context remains here to explain why the scope was narrowed before implementation.
- The API and database are implemented on separate review branches. The accepted MVP still has no accounts or authentication, and implementation progress does not expand that scope.

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

These exclusions keep the initial delivery small and testable. They may be reconsidered through a new, explicit scope decision rather than silently added to the MVP. The decision does not claim that future authentication, camera, or notification work is permanently forbidden; it requires that the added user value justify its full security, lifecycle, and testing cost before scope changes.

---

## Justification

The chosen scope concentrates on the problem's central value: recording pantry products and making expiration information reliable and visible. CRUD, validation, expiration rules, ordering, and API-failure handling form a complete vertical slice across the mobile client, API, and database. The eight Must stories have observable acceptance criteria and must be verified with suitable unit, API-integration, and UI/end-to-end tests.

Search, filtering, the empty-pantry call-to-action, and no-results feedback improve convenience but are not all release blockers. They are differentiated as Should or Could so the first increment remains feasible; US-06 is explicitly optional. Priorities should be reviewed after all Must stories meet the Definition of Done, not treated as a promise to implement every story.

The exclusions are intentional product and architecture decisions, not claims that these capabilities are impossible or undesirable. Each would introduce work that is not needed to prove the core value of recording products and seeing expiration status.

### Why login and accounts are deferred

A login screen by itself would not provide meaningful security. The current product model has no account or pantry-owner identity, the database has no ownership relation, and the API has no authentication or authorization middleware. A safe multi-user version would therefore require a coordinated vertical slice: identity and account lifecycle, password-reset or identity-provider decisions, server-side authentication, per-request authorization and ownership checks on every product endpoint, schema/data-isolation changes, credential/session lifecycle, secure token storage in the mobile application, logout and failure behavior, privacy/data-deletion policy, and tests proving that one user cannot read or mutate another user's products. Adding only the screen or a token field would create a misleading appearance of protection while leaving the shared inventory exposed. The MVP instead uses one deliberately shared demonstration pantry, restricted to a controlled local/demo environment.

### Why camera access and product photos are deferred

Manual product entry is sufficient for the core inventory workflow. Camera capture or photo selection would add runtime permission prompts and denial/retry states, device-specific testing, image sizing/compression, upload or local-file lifecycle decisions, an API/storage contract, file-size and content validation, cleanup on product deletion, and privacy handling for image metadata. Barcode scanning is a separate capability that also needs a scanning library, supported-format decisions, and an identification/catalogue source; it is not automatically delivered by adding camera permission. These requirements do not improve the MVP's essential acceptance path enough to justify their additional implementation and test surface.

### Why notifications and automatic reminders are deferred

The MVP communicates urgency by calculating and displaying expiration states when the user opens or refreshes the pantry. Reliable reminders require explicit opt-in, permission and denial flows, notification-channel/content decisions, rules for scheduling around date-only values and the America/Bogota calendar, and rescheduling/cancellation whenever a product date changes or a product is deleted. Local notifications also need lifecycle, reboot, timezone-change, and OS-background-limit testing. Push notifications add further backend scheduling, device-token handling, credentials, and a delivery provider. Implementing a button that schedules a notification without these rules would make reminder delivery unreliable and hard to verify. The first release will show expiration information in the UI and will not promise proactive alerts.

### Why offline mode, multiple pantries, and the optional portal are deferred

Offline writes require a durable local store, queueing, retry/idempotency rules, conflict resolution, and a truthful pending/synced/failed state. Multiple pantries require user/household ownership and authorization, which are outside the current data model. A separate portal would add another client and its own accessibility, routing, API integration, and deployment surface. None is necessary to demonstrate the agreed mobile CRUD and expiration flow; they remain separate future scope decisions.

### Safety boundary

Because the MVP intentionally has no authentication or user-specific isolation, the API and database must remain in a controlled local/demo environment. Do not expose the API as a public multi-user production service or store sensitive personal data. This boundary is a direct consequence of the accepted scope, not a substitute for authentication in a production system.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|-------------|------|------|-----------------------|
| Focused single-pantry inventory (chosen) | Small, traceable, testable vertical slice; matches the platform's Activity 1 scope | No personal accounts or native reminder/photo features | Chosen to keep the MVP feasible and its promises verifiable |
| Full household platform with authentication, offline cache, camera, and notifications | Broader feature set and more complete household use cases | Larger scope; more security, permission, persistence, and platform-testing work | Deferred to avoid overloading the initial MVP |
| UI-only prototype with no API/database contract | Fastest initial mockup | Does not establish end-to-end data boundaries or persistence expectations | Discarded because the proposed product architecture includes one REST API and one PostgreSQL database |

---

## Reconsideration criteria

Reopen one of these exclusions only when all of the following are true:

1. A concrete user story and acceptance criteria explain the user value and why the current MVP cannot meet it.
2. The scope/backlog register, prototype states, API/data contract, and release plan are updated together and reviewed.
3. Security/privacy review identifies the new trust boundary, permissions, data lifecycle, failure modes, and required controls.
4. Effort and verification are explicitly planned, including negative paths such as denied camera permission, disabled notifications, expired sessions, stale offline edits, or failed scheduling where relevant.
5. The change has an approved ADR or an explicit ADR amendment; implementation alone does not retroactively redefine the MVP.

This keeps future enhancements possible while preventing unplanned scope growth in the academic delivery.

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
- Documents covered by this decision set: product backlog priority register, architecture/state and navigation guidance, product domain model, API contract, prototype state specification, and MVP acceptance test matrix. Before final submission, verify that the live GitHub Project priority field and the current Figma states still match this register; this repository alone cannot prove the live board/frame state.
- Related ADRs: ADR-001 — State management pattern and ADR-003 — Navigation model.

## References

- [Activity 1 requirements published in the course platform].
- Instructor feedback and scope guidance for PantryPal, October 2026.
- Related to: [ADR-001 — State management pattern](./ADR-001-state-management-pattern.md).
