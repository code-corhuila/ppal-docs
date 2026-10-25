# API / Networking

## Client

- Use one configured Angular HttpClient setup with base URL, timeouts, and common error mapping in the mobile data layer.
- Feature pages must not call HttpClient directly; use the feature facade/use cases and repository boundary.
- Configure environment-specific API URLs. No credentials/secrets belong in the client bundle.

## Requests & retries

- Retry GET only when a bounded retry policy is appropriate. Keep retries bounded with backoff.
- Do not automatically retry POST/PUT/DELETE; show an error and preserve user-entered data where feasible if the result is uncertain.
- A network/timeout failure must never be interpreted as an empty list or successful mutation.
- Correlation/request IDs may be added by the API or client where supported, without logging sensitive data.

## Serialization

- Use DTOs and map DTOs to domain models in the data layer.
- The UI must not depend on raw API payload shapes.
- Reject malformed/unexpected payloads with a domain-facing error rather than guessing.
- The expiration date is a date-only `YYYY-MM-DD` value; do not convert it into a UTC instant.
- See [Pantry Product API Contract](./product-api-contract.md) for endpoints and the agreed error envelope.

## Status handling

| Response / condition | Meaning | App behavior |
|---|---|---|
| 2xx | Success | Map response to domain model; show success only after server confirmation |
| 400 | Validation/client error | Map field errors and preserve form values |
| 404 | Requested product is missing | Show product-not-found state and offer return to pantry |
| 5xx / timeout | Server/transient error | Surface a useful error; bounded retry only for idempotent GET requests |
| Network unavailable | No response received | Show connection error and retry action; do not show an empty pantry |

Authentication/token-refresh handling is intentionally excluded from the MVP in [ADR-002](../01-architecture/decisions/records/ADR-002-mvp-scope.md).

## Logging

- Never log credentials, tokens, full auth headers, or sensitive payloads.
- Do not log unnecessary product data. Use concise technical detail suitable for a controlled academic demo.
