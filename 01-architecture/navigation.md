# Navigation

PantryPal uses a **centralized Angular Router table** rendered with Ionic's ion-router-outlet. This is the accepted decision in ADR-003 — Navigation Model.

## Route registry

| Route | Screen | Required arguments | Guard / resolver |
|---|---|---|---|
| /pantry | Pantry product list | None | No auth guard; the MVP has no accounts or protected routes |
| /products/new | Add product form | None | CanDeactivate prompts before discarding unsaved changes |
| /products/:id/edit | Edit product form | id route parameter | Product resolver loads the current record and handles missing/error results |
| Unknown route | Not-found fallback or redirect to /pantry with safe feedback | None | Central fallback, not an authorization control |

## Rules

- Keep all route definitions in one central configuration (for example app.routes.ts); do not create screens ad hoc deep in a component tree.
- Navigate with the product identifier, not a full product object. The edit destination loads the latest record through the repository/API.
- The edit route's product resolver loads the item by ID. If the requested product cannot be loaded, show a not-found/retry state and offer a return to the pantry list.
- A successful add/edit returns to /pantry; failed validation or API calls keep the form and its entered values available for correction/retry.
- Delete confirmation is a transient Ionic alert/dialog, not a route. Use the actual selected product name. Cancel makes no change; remove the row only after API success.
- The default route leads to the pantry list. Do not add a login route or auth guard while the MVP has no authentication or protected destinations.
- Add a CanDeactivate guard to the add/edit form for dirty-form navigation; navigation must not silently persist unsaved changes.
- Respect Android hardware back and iOS back gestures. Leaving a form must not silently save unsaved changes.
- An unknown route must fail safely and return the user to a known screen.
- Keep this registry synchronized with code and the prototype.

## Navigation flow

~~~mermaid
flowchart TD
    A[App start] --> B[/pantry]
    B -->|Add product| C[/products/new]
    B -->|Edit selected product| D[/products/:id/edit]
    C -->|API confirms create| B
    D -->|API confirms update| B
    C -->|Cancel| B
    D -->|Cancel| B
    B -->|Delete selected product| E[Confirmation alert]
    E -->|Cancel| B
    E -->|Confirm and API success| B
    E -->|API failure| F[Keep product and show error]
    F --> B
~~~

> Implementation status (2026-10-30): the review branch contains these routes, the edit-product resolver, and the unsaved-changes guard. Local emulator smoke testing exercised the pantry route and CRUD navigation. That is not evidence that every navigation edge case or iOS behavior has been end-to-end tested.
