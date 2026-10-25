# Pantry Product API Contract

- **Date:** 2026-10-25
- **Status:** Proposed contract baseline for MVP implementation
- **Related decisions:** ADR-002 (MVP Scope), ADR-003 (Navigation Model)
- **Authentication:** No authentication in the MVP; controlled demonstration environment only.

This document specifies the intended REST contract. It is not evidence that the API endpoints have already been implemented.

## Resource

Base path: `/api`

Product fields and fixed enum values are defined in [Product Domain Model](./product-domain-model.md). The API is the source of truth and is the only component that reads/writes PostgreSQL. The mobile app must not connect directly to the database.

## Endpoints

| Method | Path | Purpose | Success |
|---|---|---|---|
| GET | `/api/products` | Return all pantry products | `200 OK`, JSON array |
| GET | `/api/products/{id}` | Return one product for the edit form | `200 OK`, JSON object |
| POST | `/api/products` | Create a product | `201 Created`, created object |
| PUT | `/api/products/{id}` | Replace editable fields of a product | `200 OK`, updated object |
| DELETE | `/api/products/{id}` | Delete a product | `204 No Content` |

US-05 search and US-06 status filters are not required by the MVP contract. Do not add query parameters for them unless the associated story is included through explicit scope review.

## Product representation

Example response object (illustrative values):

~~~json
{
  "id": "product-id",
  "name": "Whole Milk",
  "quantity": 1,
  "unit": "unit",
  "category": "dairy",
  "expirationDate": "2026-10-28",
  "createdAt": "2026-10-25T15:00:00Z",
  "updatedAt": "2026-10-25T15:00:00Z"
}
~~~

- `id`, `createdAt`, and `updatedAt` are server-managed.
- `expirationDate` is a date-only string or null; do not serialize it as midnight UTC.
- POST/PUT accepts only editable fields: `name`, `quantity`, `unit`, `category`, `expirationDate`.
- The list endpoint may return records in any order; the client domain/presentation flow applies expiration-date ascending order and places undated products last.
- The client derives expiration status; it is not included in the request or stored as an authoritative field.

## Request examples

Create/update body:

~~~json
{
  "name": "Whole Milk",
  "quantity": 1,
  "unit": "unit",
  "category": "dairy",
  "expirationDate": "2026-10-28"
}
~~~

To clear a previously set expiration date, send `"expirationDate": null`. For category/unit, send the exact wire values from the domain model. Omit or reject unknown fields according to the server validation implementation; clients must not be allowed to set server-managed timestamps or IDs.

## Errors

Use a consistent JSON envelope:

~~~json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Review the highlighted fields.",
    "fieldErrors": {
      "name": "Name is required."
    }
  }
}
~~~

`fieldErrors` is optional and appears when field-level feedback applies.

| Status | Meaning | Expected client behavior |
|---|---|---|
| `400 Bad Request` | Invalid/malformed input | Show field errors; preserve entered values |
| `404 Not Found` | Requested product ID does not exist | Show a product-not-found message; return to the pantry list when appropriate |
| `500 Internal Server Error` | Unexpected server error | Show a generic error and safe retry where appropriate |
| Network failure / timeout | Server unavailable or response not received | Show connection error and retry; never interpret as empty data or success |

Authentication-specific responses such as 401 token refresh are out of scope for this no-auth MVP. Do not build token interceptors or login handling as a hidden dependency.

## Client behavior and consistency

- GET requests may be retried with a bounded policy because they are idempotent. Do not automatically retry POST/PUT/DELETE because a timeout can leave the operation outcome uncertain.
- Show mutation success only after an explicit successful server response.
- A failed DELETE leaves the product visible in the UI.
- GET /api/products returning `200 []` is the only basis for the true empty-pantry state.
- The client must not treat a failed/malformed response as a successful empty list.
- Configure a base URL per environment; do not hard-code production secrets into the app. The unauthenticated API must remain restricted to a controlled demo environment.
