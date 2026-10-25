# Navigation

PantryPal uses a **centralized Angular Router table** rendered with Ionic's ion-router-outlet. This is the accepted decision in ADR-003 — Navigation Model.

## Route registry

| Route | Screen | Required arguments | Authentication |
|---|---|---|---|
| /pantry | Pantry product list | None | Not applicable; no authentication in the MVP |
| /products/new | Add product form | None | Not applicable; no authentication in the MVP |
| /products/:id/edit | Edit product form | id route parameter | Not applicable; no authentication in the MVP |
| Unknown route | Not-found fallback or redirect to /pantry with safe feedback | None | Not applicable; no authentication in the MVP |

## Rules

- Keep all route definitions in one central configuration (for example app.routes.ts); do not create screens ad hoc deep in a component tree.
- Navigate with the product identifier, not a full product object. The edit destination loads the latest record through the repository/API.
- If the requested product ID cannot be loaded, show a clear message and offer a return to the pantry list.
- A successful add/edit returns to /pantry; failed validation or API calls keep the form and its entered values available for correction/retry.
- Delete confirmation is a transient Ionic alert/dialog, not a route. Use the actual selected product name. Cancel makes no change; remove the row only after API success.
- The default route leads to the pantry list. Do not add a login route or auth guard while the MVP has no authentication or protected destinations.
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

> This describes the intended MVP design. The route table and flows are not a claim that the app screens have been implemented.
