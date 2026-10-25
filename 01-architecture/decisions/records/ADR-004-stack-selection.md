# ADR-004 — Technology Stack Selection

- **ID:** ADR-004
- **Date:** 2026-10-25
- **Status:** Proposed — awaiting verification of the local application package versions
- **Authors:** Gustavo Gallego

---

## Context

PantryPal is an individual academic project for a small hybrid mobile pantry inventory. The project needs reusable mobile UI, a clear separation between presentation/domain/data, REST API integration, predictable routing, testable forms, and a maintainable build with a limited delivery window. The current target stack is Ionic + Angular + Capacitor. The Unit 2 H2 guide requires this choice to be justified against Angular, React, and Vue using the same weighted criteria, with alternatives, consequences, and a verification step.

The organization repository `code-corhuila/ppal-app` currently has no `package.json` on its default branch, so the exact installed versions must be verified from the developer's local application workspace before this ADR can be marked Accepted. Do not copy versions from the course reference app.

**Known constraints:**
- One individual developer and a focused MVP.
- The target platform is a hybrid mobile application built with Ionic and Capacitor.
- The current accepted state approach is the feature facade / MVVM-style pattern with immutable screen state and Angular Signals (ADR-001).
- The current accepted MVP excludes login/accounts. Token refresh is not current MVP behavior; any future authentication change requires explicit scope and security review.
- The current accepted offline policy is online-only with visible error/retry; local offline cache and synchronization are deferred by ADR-002.

---

## Decision

**Proposed decision:** keep Ionic + Angular + Capacitor as PantryPal's technology stack, use Angular Signals with a feature facade/view-model for UI state, RxJS at asynchronous HTTP boundaries where streams are useful, Angular Router for centralized navigation, Reactive Forms for field-level form behavior, and one Angular HttpClient configuration in the data layer.

This proposal becomes **Accepted** after the local `package.json` versions and the verification command are recorded below.

### Weighted decision matrix

Score each alternative from 1 (poor fit) to 5 (excellent fit). Weighted score = weight × score. The criteria are weighted for this project's requirements, not claimed as a universal ranking of the frameworks.

| Criterion | Weight | Angular | React | Vue |
|---|---:|---:|---:|---:|
| Ionic integration and mobile component fit | 5 | 5 (25) | 4 (20) | 4 (20) |
| Built-in structure for routing, dependency injection, forms, and HTTP | 4 | 5 (20) | 3 (12) | 4 (16) |
| TypeScript conventions and maintainability across feature layers | 3 | 5 (15) | 4 (12) | 4 (12) |
| Test seams and substitution of data/repository dependencies | 3 | 5 (15) | 3 (9) | 3 (9) |
| Delivery speed for this existing project scaffold | 3 | 4 (12) | 3 (9) | 3 (9) |
| **Total (maximum 90)** | **18** | **87** | **62** | **66** |

**Interpretation:** Angular scores highest for PantryPal because routing, dependency injection, reactive forms, and HttpClient are part of the same framework ecosystem and fit the existing Ionic + Angular scaffold. React is capable, but would require choosing and governing more separate libraries and conventions. Vue is also viable, with Vue Router and Pinia available, but switching would discard the existing scaffold and require a new integration decision. Scores are a reasoned project-specific evaluation and should be adjusted if the developer's actual experience or local implementation evidence differs.

### Complementary libraries and patterns

- **Ionic Angular:** mobile UI components, platform mode behavior, and layout primitives.
- **Angular Signals + feature facade/view-model:** one agreed UI-state pattern; immutable state snapshots and explicit loading/success/empty/error transitions. NgRx is not included because the MVP is small and there is no demonstrated need for a global store.
- **RxJS:** asynchronous HTTP streams and the Angular HttpClient boundary; it does not replace the chosen UI-state pattern.
- **Angular Router:** one central route registry; identifiers, not complete product objects, cross route boundaries.
- **Angular Reactive Forms:** field-level form status and user feedback; the domain remains the source of business validation rules.
- **Angular HttpClient:** one configured client and shared error mapping in the data layer.

### Security and offline scope alignment

The MVP has no login or user identity, so it has no access token, refresh token, login guard, or logout flow to implement in this scope. It must remain a controlled demonstration environment and must not be exposed as a public multi-user production service. If authentication is introduced in a later scope decision, the design must use a secure-storage adapter (not localStorage or Capacitor Preferences for credentials), a centralized interceptor chain, serialized token refresh, and at most one retry of the original request.

The selected MVP connectivity strategy is online-only: failed requests show a recoverable error, not an empty list or fake success. A read-through cache/offline mode is deferred by ADR-002; this is a deliberate scope trade-off and does not claim to satisfy an offline-cache implementation requirement.

### Version record — required before acceptance

Record actual values from the local application's `package.json`; do not use the sample application's versions.

| Package | Actual local version |
|---|---|
| `@ionic/angular` | **To verify** |
| `@angular/core` | **To verify** |
| `@angular/router` | **To verify** |
| `@capacitor/core` | **To verify** |
| `@capacitor/cli` | **To verify** |
| `typescript` | **To verify** |
| Test runner | **To verify** |

**Verification command:** pending local workspace verification. After recording the versions, run the existing build/lint/test commands and link or record the actual results; do not claim tests passed without execution evidence.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|-------------|------|------|-----------------------|
| **Ionic + Angular + Capacitor (proposed)** | Integrated DI, routing, Reactive Forms, HttpClient, Signals, and an Ionic integration matching the current scaffold | Framework conventions can feel more structured and require learning Angular-specific patterns | Best fit for the existing PantryPal scaffold and the assignment's layered-architecture goals |
| Ionic React + React + Capacitor | Flexible composition and a large React ecosystem | Would require selecting and governing separate choices for routing, state, forms, and dependency substitution; migrating the current scaffold adds work | Discarded for this MVP because it adds migration and governance cost without a current product benefit |
| Ionic Vue + Vue + Capacitor | Approachable component model, Vue Router and Pinia ecosystem | Would require a scaffold migration and new decisions for DI/test seams, forms, HTTP boundaries, and state conventions | Discarded because it provides no clear benefit over the established Angular scaffold for this limited schedule |

---

## Consequences

**Positive:**
- The project uses one documented framework stack and one UI-state pattern.
- The data boundary can be tested with repository doubles without rendering Ionic screens.
- The same route, validation, and HTTP conventions can be used consistently across features.

**Negative / Trade-offs:**
- Angular conventions and Signals require deliberate learning and consistency.
- The MVP does not demonstrate account authentication or offline caching; those choices must be presented honestly as scope limitations, not implemented features.
- This matrix is still Proposed until the local package versions and verification evidence are recorded.

**Impact on the system:**
- Affected areas: `ppal-app`, state management, route table, product forms, shared UI tokens, API client, test strategy.
- Documents to align: ADR-001, ADR-002, ADR-003; `02-code-and-ui/design-system.md`, `accessibility.md`, `i18n.md`; `03-api-and-data/api-networking.md`, `authentication.md`, `offline-sync.md`; test strategy.
- Related ADRs: ADR-001 — State Management Pattern; ADR-002 — MVP Scope; ADR-003 — Navigation Model.

## References

- Angular. (n.d.). *Angular documentation*. https://angular.dev/
- Ionic. (n.d.). *Ionic Framework documentation*. https://ionicframework.com/docs/
- Capacitor. (n.d.). *Capacitor documentation*. https://capacitorjs.com/docs
- React. (n.d.). *React documentation*. https://react.dev/
- Vue.js. (n.d.). *Vue.js documentation*. https://vuejs.org/guide/
- Related to: ADR-001, ADR-002, ADR-003.
