# API / Networking

## Client boundary

PantryPal uses one configured Angular `HttpClient` setup with the API base URL, timeout policy, request metadata, and common error mapping. Feature pages must not instantiate or call `HttpClient` directly; they use the pantry feature facade/use cases and the repository boundary.

The client configuration and active interceptors are registered in one place. For the no-auth MVP, they cover:
- request metadata/correlation where available;
- bounded request timeout behavior;
- mapping low-level HTTP/network failures into stable application errors.

An Authorization/token interceptor is deliberately not active because the accepted MVP has no login, identity, or token. Do not fabricate token handling for an API that does not issue tokens. If authentication is explicitly added in a future scope, it must use a centralized interceptor chain and secure token storage, not per-page headers.

## Requests, errors, and retries

- Configure environment-specific API URLs. No credentials/secrets belong in the client bundle.
- Retry idempotent GET requests only with a bounded policy and backoff where appropriate.
- Do not automatically retry POST/PUT/DELETE: a timeout can leave the mutation outcome uncertain.
- A network/timeout failure is an explicit error, never an empty pantry or successful mutation.
- Preserve entered form values after validation/API failures where feasible; success feedback is shown only after server confirmation.
- Convert HTTP/network/parsing failures into application/domain-facing errors before they reach a page; UI messages must include a sensible recovery action.

## DTOs and mapping

- DTOs mirror the agreed API JSON contract. A mapper converts DTOs to the domain entity and rejects malformed or unexpected payloads instead of guessing.
- The UI must not depend on raw payload shapes.
- `expirationDate` is a date-only `YYYY-MM-DD` value or null; do not convert it to midnight UTC.
- The mobile app connects to the REST API, never directly to PostgreSQL.

## Error mapping

| Condition | Client behavior |
|---|---|
| 2xx | Map the response; only then display mutation success |
| 400 | Map validation/field errors; preserve the form |
| 404 | Show product-not-found feedback and a route back to the pantry |
| 5xx / timeout | Show a service error with a safe retry option where appropriate |
| Network unavailable | Show connection error/retry and do not claim the pantry is empty |

## Authentication expansion — not MVP behavior

If a future accepted decision introduces authentication, require:
- a dedicated secure-storage adapter backed by platform secure storage (not `localStorage` or plain Capacitor Preferences for credentials);
- a centralized auth interceptor;
- serialized/single-flight refresh when concurrent requests receive 401;
- at most one retry of the original request after successful refresh;
- clearing credentials and private cache on logout/refresh failure;
- tests for concurrent 401s, refresh failure, and no infinite retry loops.

These are future security requirements; the current no-auth MVP has no token refresh path to run or claim as tested.

## Logging

- Never log passwords, tokens, full authorization headers, or sensitive payloads.
- Do not log unnecessary product data; use concise diagnostics appropriate to a controlled academic demonstration.
