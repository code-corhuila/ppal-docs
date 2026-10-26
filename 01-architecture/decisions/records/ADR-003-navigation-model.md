# ADR-003 — Navigation Model

- **ID:** ADR-003
- **Date:** 2026-10-25
- **Status:** Accepted
- **Authors:** Gustavo Gallego

---

## Context

PantryPal's MVP has a small number of screens: the pantry product list, an add/edit product form, and a delete confirmation. There are no accounts or login screens. Navigation must remain easy to reason about, handle Android hardware back and iOS back gestures, and allow the edit form to load the current record from the API instead of passing a possibly stale product object between pages.

**Known constraints:**
- Ionic + Angular + Capacitor is the chosen client stack.
- The MVP has one demonstration pantry, no authentication, and online-only operations.
- Delete confirmation is a transient interaction rather than a destination that needs a shareable URL.
- The current repository is an initial scaffold; this records the intended routing design and does not claim the routes are already implemented.

---

## Decision

**We decided:** use a single centralized Angular Router configuration rendered through Ionic's router outlet. Use named paths for the pantry list and product form; use an Ionic confirmation alert for deletion rather than a separate route.

### Route registry

| Route | Screen | Required arguments | Guard / resolver |
|---|---|---|---|
| /pantry | Pantry list | None | No auth guard: the accepted MVP has no login or protected routes |
| /products/new | Add product form | None | CanDeactivate guard prompts before losing unsaved changes |
| /products/:id/edit | Edit product form | id route parameter | Product resolver loads the latest record; missing/error result shows not-found/retry feedback |
| Unknown route | Not-found fallback or redirect to /pantry with safe feedback | None | Central fallback; not an authorization control |

Implementation rules:
- Declare the route table centrally (for example in app.routes.ts) and render routed pages with ion-router-outlet.
- Use /pantry as the default application destination; do not add /login or an authentication guard to this MVP. The route registry must nevertheless name the form-leave guard and the edit-product resolver.
- Pass only the product identifier to the edit route. A resolver loads the record from the repository/API when the destination opens; if it cannot be found, show clear not-found/retry feedback and offer return to the list.
- After a successful create or edit, navigate back to /pantry and show confirmation. On validation or API failure, keep the form and entered values available for correction/retry.
- Present delete confirmation as an accessible Ionic alert/dialog with the selected product's actual name. Cancel closes it without changing data. On confirmed deletion, update the list only after API success; on failure keep the record and display an error.
- Use a CanDeactivate guard for unsaved add/edit form changes: confirm the user's intent before leaving a dirty form, and never auto-save as a side effect of navigation.
- Respect Android hardware back and iOS back gestures. Cancel/back from an unsaved form must not persist changes.
- Use the framework's normal URL parsing; do not create a second, feature-specific deep-link parser.

**Justification:** a small centralized route table fits three primary destinations, preserves expected mobile back behavior, guards against losing unsaved form input, and resolves the requested product before rendering edit mode. It avoids adding an auth guard that has no protected route to guard in the current MVP.

---

## Evaluated alternatives

| Alternative | Pros | Cons | Reason for discarding |
|-------------|------|------|-----------------------|
| Central Angular Router table + Ionic router outlet (chosen) | Standard Ionic/Angular flow, supports browser history and platform back behavior | Requires route configuration and handling invalid IDs | Fits the target stack and small screen set |
| Change screens with local booleans / ad hoc component creation | Quick for a static prototype | Navigation is hidden in component state; back/deep links and tests become fragile | Rejected for maintainability and predictable routing |
| Make the delete confirmation a full route | Addressable route and standalone screen | Unnecessary navigation and a poor fit for a transient confirmation | Use an Ionic alert/dialog instead |
| Add login routes and authentication guards now | Could support multiple personal pantries later | Contradicts the accepted no-auth MVP and adds unnecessary scope | Explicitly excluded by ADR-002 |

---

## Consequences

**Positive:**
- There is one route registry that documentation and code can share.
- Product edit behavior is deterministic and reloads the chosen record by ID.
- Confirm/cancel/failure behavior is clear and testable.

**Negative / Trade-offs:**
- The edit page needs a loading and product-not-found state.
- Without authentication, the API and app are suitable only for a controlled demo environment; route handling is not an access-control boundary.

**Impact on the system:**
- Affected features/screens: pantry list, add/edit form, delete confirmation overlay, and unknown-product/route error handling.
- Documents to keep aligned: navigation.md, prototype-state-spec.md, API contract, and UI tests.

## References

- Angular Router documentation: https://angular.dev/guide/routing
- Ionic Angular navigation documentation: https://ionicframework.com/docs/angular/navigation
- Related to ADR-002 — MVP Scope and ADR-001 — State Management Pattern.
