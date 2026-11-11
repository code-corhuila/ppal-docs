# 03 — API & Data

> Rules for how the app consumes the REST API and how data is represented. The MVP is online-only and has no user accounts or authentication.

## Why this section exists

The API and data boundary must be explicit so that mobile UI, business rules, persistence, and error handling remain consistent and independently testable.

---

## Documents in this section

| File | Purpose |
|------|---------|
| [product-api-contract.md](./product-api-contract.md) | MVP endpoints, request/response shapes, errors, and client behavior |
| [product-domain-model.md](./product-domain-model.md) | Product fields, allowed values, validation, and expiration-date rules |
| [api-networking.md](./api-networking.md) | HTTP client, bounded retries, and error mapping |
| [authentication.md](./authentication.md) | Confirms authentication is excluded now and records future security constraints |
| [persistence.md](./persistence.md) | Notes on local persistence; no mobile offline cache is required for the MVP |
| [offline-sync.md](./offline-sync.md) | Online-only behavior and explicitly deferred sync strategies |
| [_template-endpoint-integration.md](./_template-endpoint-integration.md) | Template to document consuming one backend endpoint |
| [_template-entity.md](./_template-entity.md) | Reference template; not a declaration of local MVP storage |

---

## Boundaries

- The Ionic + Angular client talks to the REST API; it does not access PostgreSQL directly.
- The API alone validates requests and performs database reads/writes; client validation improves UX but is not a security boundary.
- Product expiration status is calculated from the calendar date in America/Bogota and is not persisted as a separate field.
- GET `/api/products` returning `200 []` is distinct from a network or API failure.
- No login, accounts, local offline cache, or offline synchronization is promised in this MVP.

## Cross-section dependencies

| Section | Relationship |
|---|---|
| `01-architecture/` | API/data implementations satisfy domain repository contracts |
| `00-governance/security-rules.md` | Governs transport and safe handling |
| `04-quality/` | Endpoint and validation behavior require unit/integration/UI tests |

