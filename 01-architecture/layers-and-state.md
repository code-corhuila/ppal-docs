# Layers & State

## Layers

| Layer | PantryPal responsibility | Allowed dependencies |
|---|---|---|
| Presentation | Ionic pages/components, accessible UI, form feedback, and forwarding user actions | Domain/use-case interfaces and feature state facades |
| Domain | Product entity, allowed units/categories, validation, expiration classification, repository interfaces, and use cases | No outward dependencies |
| Data | Angular HTTP data source, DTO mapping, and repository implementations | Domain contracts; Angular/HTTP infrastructure may be used here |
| Core / shared | App-wide route configuration, environment/API configuration, reusable UI primitives | No feature-specific business rules |

## Dependency direction

~~~text
Presentation ─▶ Domain ◀─ Data
~~~

- Domain has **zero** outward dependencies: no HTTP client, database driver, Ionic component, or Angular UI imports.
- Presentation must not import an HTTP service or database/data-source implementation directly.
- Data implements domain repository interfaces; domain never depends on concrete data implementations.
- The mobile app communicates with the REST API; it does not connect directly to PostgreSQL.
- The API is the source of truth. The MVP is online-only; network failure produces an explicit error and retry path, not an empty list or successful operation.

## State management — accepted decision

PantryPal uses an MVVM-style feature facade/view-model with an immutable state snapshot exposed through Angular read-only Signals, as documented in ADR-001.

- Use one agreed pattern; do not introduce NgRx or a second state-management library for the MVP without a new ADR.
- A page renders state and forwards user actions; it does not own business rules, expiration calculation, or direct API calls.
- Replace state objects on transitions; do not mutate arrays or nested product objects in place.
- Use computed values for derived UI state when helpful. Keep domain rules in independently testable pure functions/use cases.
- Model loading, success, empty response, validation, saving, and API/network errors explicitly. Empty data and failed requests are different states.
- API calls may use Angular HttpClient and RxJS observables inside the data layer; this does not replace the app's agreed UI-state pattern.
- No offline cache, queued writes, or offline synchronization are promised in the MVP.

## Use cases

One use case represents one business action (for example ListProducts, CreateProduct, UpdateProduct, DeleteProduct, ClassifyExpiration). Keep use cases thin and independently testable: they orchestrate repositories and contain no UI concerns or framework-specific component types.

## MVP product rules

- Require a product name that is not blank after trimming.
- Quantity must be greater than zero. Whole-unit and package quantities are integers; weight/volume quantities may be decimal according to the agreed unit list.
- Category and unit values must come from the documented fixed lists. The API repeats validation independently.
- Expiration date is optional; if present, it is a valid calendar date. Past dates are allowed so already-expired stock can be recorded.
- Calculate expiration status by calendar date in America/Bogota: Expired when before today; Expiring Soon from today through the next three calendar days inclusive; Not Expiring Soon after that; No Expiration Date when absent.
