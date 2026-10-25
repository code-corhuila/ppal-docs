# PantryPal Product Backlog Priorities

- **Date:** 2026-10-25
- **Owner:** Gustavo Gallego
- **Scope basis:** [ADR-002 — Minimum Viable Product scope](../01-architecture/decisions/records/ADR-002-mvp-scope.md)
- **Status:** Proposed baseline for synchronizing with the GitHub Project `ppal-backlog`

## Prioritization method

This backlog uses MoSCoW priorities. A story is **Must** only when the MVP cannot be considered complete without its core user value. **Should** stories are valuable but do not block the first delivery if time is constrained. **Could** stories are optional enhancements and are only started when all Must stories meet the Definition of Done and time remains. The priority is not an estimate of effort.

## Prioritized user stories

| Priority | Story | GitHub issue | User value | Rationale / dependency |
|---|---|---|---|
| **Must** | **US-01 — View pantry products** | [#1](https://github.com/code-corhuila/ppal-app/issues/1) | Read the pantry inventory from the REST API and distinguish a successful empty response from a failure. | Foundation for all product actions; validates the app-to-API path. |
| **Must** | **US-02 — Add a product** | [#02](https://github.com/code-corhuila/ppal-app/issues/2) | Create a product with valid fields and confirm success only after the API responds successfully. | Core inventory capability; depends on the API/data contract and US-09 validation. |
| **Must** | **US-03 — Edit a product** | [#03](https://github.com/code-corhuila/ppal-app/issues/3) | Change an existing product, including setting or clearing its optional expiration date. | Completes the essential update workflow. |
| **Must** | **US-04 — Delete a product** | [#04](https://github.com/code-corhuila/ppal-app/issues/4) | Confirm deletion and remove the item from the UI only after the API confirms success. | Completes CRUD and avoids false success. |
| **Must** | **US-07 — Calculate expiration status** | [#07](https://github.com/code-corhuila/ppal-app/issues/7) | Show Expired, Expiring Soon, Not Expiring Soon, or No Expiration Date using the agreed calendar-date rules. | Central problem value of PantryPal; must be consistent and testable. |
| **Must** | **US-08 — Sort by expiration date** | [#08](https://github.com/code-corhuila/ppal-app/issues/8) | Put dated products in ascending expiration-date order and undated products last. | Makes the core expiration information actionable. |
| **Must** | **US-09 — Validate product data** | [#09](https://github.com/code-corhuila/ppal-app/issues/9) | Reject invalid names, quantities, units, categories, and dates in the client and independently in the API. | Protects data integrity and supports reliable API behavior. |
| **Must** | **US-10 — Handle API failures** | [#10](https://github.com/code-corhuila/ppal-app/issues/10) | Show an understandable error and recovery/retry path; never report an operation as successful before server confirmation. | Essential for truthful feedback in an API-backed app. |
| **Should** | **US-05 — Search by product name** | [#05](https://github.com/code-corhuila/ppal-app/issues/5) | Find products using a case-insensitive name search. | Useful convenience, but the core inventory flow works without search. |
| **Should** | **US-11 — Show the empty-pantry state** | [#11](https://github.com/code-corhuila/ppal-app/issues/11) | When the API successfully returns zero products, show a helpful message and an Add Product action. | Improves first-use guidance; US-01 must still distinguish empty data from an API failure. |
| **Should** | **US-12 — Show no-results feedback** | [#12](https://github.com/code-corhuila/ppal-app/issues/12) | Explain when a product-name search matches no records and provide a way to clear the search. This story depends on US-05; status filters are not included unless US-06 is promoted from Could. | Complements US-05; must not be confused with a truly empty pantry. US-06 filtering remains optional. |
| **Could** | **US-06 — Filter by expiration status** | [#06](https://github.com/code-corhuila/ppal-app/issues/6) | Filter products by All Products, Expiring Soon, Expired, or No Expiration Date. | Adds extra UI/state combinations; defer until all Must stories pass and capacity is confirmed. |

## Recommended implementation order

Priority and implementation order are related but not identical. Deliver the Must stories in dependency-aware increments:

1. **Data path:** agree the product/API contract and verify successful list loading plus loading/error behavior (US-01, US-10).
2. **Data integrity and create:** implement shared field rules and create flow (US-09, US-02).
3. **Complete CRUD:** edit and delete with server-confirmed outcomes (US-03, US-04).
4. **Expiration behavior:** implement and test the date classification and default ordering together (US-07, US-08).
5. **Release verification:** exercise success, empty response, invalid input, network/API failure, and failed mutations before marking the Must scope done.
6. **After Must:** consider search and the associated no-results feedback (US-05, US-12), then the empty-pantry call-to-action (US-11), according to time and prototype feedback. Attempt US-06 only if meaningful time remains.

This sequence does not mean that tests or error handling should be postponed until the end; each story must be tested as it is implemented. It identifies a sensible integration order.

## Release and change-control rule

- The MVP release is blocked by any incomplete Must story or unmet Definition of Done.
- Should and Could stories are not implicitly promised for the first MVP. Review remaining capacity after the Must scope is verified.
- Do not promote a story to Must without updating this register, ADR-002, and the `ppal-backlog` Project together through an explicit scope review.
- Features excluded by ADR-002 (including accounts/authentication, camera/photos, notifications/reminders, and offline synchronization) remain outside this backlog baseline. Reconsider them only through a new scope decision.

## Traceability note

The repository README names GitHub Project `ppal-backlog` as the operational backlog. This file records the proposed priority baseline for review and traceability; the corresponding Project item priority fields and ordering must be synchronized with this table before treating the board as updated.
