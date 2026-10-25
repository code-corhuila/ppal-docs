# ADR-004 — Technology Stack Selection

- **ID:** ADR-004
- **Date:** 2026-10-25
- **Status:** Accepted
- **Authors:** Gustavo Gallego

---

## Context

PantryPal is an individual academic project for a small hybrid mobile pantry inventory. The project needs reusable mobile UI, a clear separation between presentation/domain/data, REST API integration, predictable routing, testable forms, and a maintainable build with a limited delivery window. The current target stack is Ionic + Angular + Capacitor. The Unit 2 H2 guide requires this choice to be justified against Angular, React, and Vue using the same weighted criteria, with alternatives, consequences, and a verification step.

The declared package version specifications were verified against the developer's local `D:\\Corhuila\\Mobile\\ppal-app\\package.json` on 2026-10-25. The organization repository `code-corhuila/ppal-app` does not currently expose that `package.json` on its default branch, so these values are local-workspace evidence and should be synchronized with the shared repository when its scaffold is pushed. The `^` and `~` prefixes are package version ranges, not claims about the exact resolved versions in `node_modules`. Do not copy versions from the course reference app.

**Known constraints:**
- One individual developer and a focused MVP.
- The target platform is a hybrid mobile application built with Ionic and Capacitor.
- The current accepted state approach is the feature facade / MVVM-style pattern with immutable screen state and Angular Signals (ADR-001).
- The current accepted MVP excludes login/accounts. Token refresh is not current MVP behavior; any future authentication change requires explicit scope and security review.
- The current accepted offline policy is online-only with visible error/retry; local offline cache and synchronization are deferred by ADR-002.

---

## Decision

**Decision:** keep Ionic + Angular + Capacitor as PantryPal's technology stack, use Angular Signals with a feature facade/view-model for UI state, RxJS at asynchronous HTTP boundaries where streams are useful, Angular Router for centralized navigation, Reactive Forms for field-level form behavior, and one Angular HttpClient configuration in the data layer. This is a project-specific selection, not a claim that Angular is universally superior.

### Weighted decision matrix

This matrix carries forward the project-specific assessment already submitted in Activity 1 (PantryPal Technical Design, Section 2.2). The weights reflect PantryPal's course stack, short implementation window, and need for integrated routing/forms/dependency injection; the scores are not a universal ranking.

| Criterion | Weight | Angular | React | Vue |
|---|---:|---:|---:|---:|
| Alignment with the course stack | 30% | 5 (30) | 3 (18) | 3 (18) |
| Integrated routing, guards, forms, and dependency injection | 25% | 5 (25) | 3 (15) | 3 (15) |
| Type-safe maintainability | 20% | 5 (20) | 4 (16) | 4 (16) |
| Fast delivery for this project | 15% | 4 (12) | 4 (12) | 5 (15) |
| Ionic ecosystem fit | 10% | 5 (10) | 5 (10) | 5 (10) |
| **Weighted total (maximum 100)** | **100%** | **97/100** | **71/100** | **74/100** |

**Interpretation:** Angular remains the preferred option because it aligns with the course's Ionic + Angular + Capacitor stack and supplies consistent conventions for routes, dependency injection, forms, and HTTP. React remains capable, but the project would need to choose and govern more supporting libraries. Vue is also viable, but its score does not outweigh the effort of replacing the current Angular scaffold. These scores preserve the rationale documented in the Activity 1 submission and can be revisited if implementation evidence materially changes the assumptions.

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

### Version record — local package.json declarations

The values below are the declarations reported from the local application's `package.json`. Ranges are preserved as declared; the exact resolved versions can be checked separately with `npm ls --depth=0`.

| Package | Actual local version |
|---|---|
| `@ionic/angular` | `^9.0.0` |
| `@angular/core` | `22.2.3` |
| `@angular/router` | `22.2.3` |
| `@capacitor/core` | `8.5.3` |
| `@capacitor/cli` | `8.5.3` |
| `typescript` | `~6.0.0` |
| `vitest` | `^4.1.12` |

**Version check command executed locally:** the developer ran a Node command that reads the above declarations from `package.json`. The command verifies declared package specs, not the exact resolved install. For exact resolved versions use `npm ls @ionic/angular @angular/core @angular/router @capacitor/core @capacitor/cli typescript vitest`.

**Initial environment verification:** the previous local check reported the app build and lint passing. The test suite has not yet been run, so no test-pass claim is made. Run the existing test command before treating test execution as verified.

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
- Build and lint were reported passing during the initial local scaffold verification; automated tests remain unexecuted and are still a validation item.

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
