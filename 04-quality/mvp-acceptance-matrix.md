# PantryPal MVP Acceptance Test Matrix

- **Date:** 2026-10-25
- **Status:** Planned — test execution is pending product implementation
- **Scope basis:** ADR-002 and Product Backlog Priorities
- **Test environment:** To be documented when the API, database, and mobile features are implemented. Do not treat this matrix as proof of passing tests.

| Test ID | Story / priority | Level | Scenario | Expected result |
|---|---|---|---|---|
| TC-01 | US-01 / Must | API + UI | API returns a non-empty product array | List renders the received products; loading state ends |
| TC-02 | US-01 / Must | API + UI | API returns HTTP 200 with an empty array | The empty response is visibly distinct from an error; the dedicated empty message and Add Product call-to-action are verified separately in TC-20 |
| TC-03 | US-10 / Must | UI | Initial GET fails or times out | Load error and retry action appear; app does not claim pantry is empty |
| TC-04 | US-02 / Must | API + UI | Submit a valid product and receive 201 | New item appears after server confirmation; success feedback appears |
| TC-05 | US-09 / Must | Unit + UI | Name is blank or whitespace-only | Client rejects submission; field-level error appears |
| TC-06 | US-09 / Must | Unit + API | Quantity is zero, negative, or a fraction for Units/Packages | Client/API reject the invalid value |
| TC-07 | US-09 / Must | Unit + API | Unit or category is not in the allowed list | Request is rejected; no product is created |
| TC-08 | US-09 / Must | Unit + API | Expiration date is not a valid calendar date | Validation error; form values remain available for correction |
| TC-09 | US-07 / Must | Unit | Expiration date is yesterday in America/Bogota | Status is Expired |
| TC-10 | US-07 / Must | Unit | Expiration date is today | Status is Expiring Soon |
| TC-11 | US-07 / Must | Unit | Expiration date is today + 3 calendar days | Status is Expiring Soon |
| TC-12 | US-07 / Must | Unit | Expiration date is today + 4 calendar days | Status is Not Expiring Soon |
| TC-13 | US-07 / Must | Unit | Expiration date is absent/null | Status is No Expiration Date |
| TC-14 | US-08 / Must | Unit + UI | Mix dated products and products with no date | Dated products ascend by date; undated products appear last |
| TC-15 | US-03 / Must | API + UI | Edit an item by its ID and clear expiration date | Correct product is loaded; update is persisted after 200; cleared date remains null |
| TC-16 | US-04 / Must | API + UI | Confirm deletion and receive successful response | Item is removed from list only after API success |
| TC-17 | US-04 / Must | UI | DELETE fails | Item remains visible and the user receives error/retry feedback |
| TC-18 | US-10 / Must | UI | POST/PUT fails after client validation | No success toast or navigation; entered values are preserved for retry |
| TC-19 | US-05 / Should | Unit + UI | Search has no matches | “No products found” appears with a clear-search action, distinct from the empty pantry |
| TC-20 | US-11 / Should | UI | Delete the last item and refresh successfully | Empty pantry message and Add Product action appear |
| TC-21 | US-12 / Should | UI | Search has no matches while pantry has records | No-results message is shown, and clearing search restores the list |
| TC-22 | US-03 / Must | API + UI | Edit route receives an unknown product ID | Product-not-found feedback appears; user can return to pantry |
| TC-23 | US-02–04 / Must | API + UI | Mutation returns an error response | UI only reflects successful server-confirmed changes; no optimistic false success |

## Execution rules

- Run unit tests with fixed dates/clock for expiration boundary cases.
- Run API contract/integration tests with a controlled test database or isolated doubles; do not use sensitive data.
- Run UI tests for loading, empty, error/retry, validation, saving, mutation failure, and delete confirmation states.
- Record actual command, environment, result, and date in the activity evidence when tests are executed. Until then, the status remains Planned.
