# Client-Side Persistence

## Current MVP decision

The accepted PantryPal MVP does **not** use a local product database, offline cache, offline write queue, or synchronization. The REST API and PostgreSQL database are the source of truth. This is intentional online-only behavior recorded in ADR-002, not an incomplete selection between SQLite, Realm, or another local store.

A failed request must remain visible as a recoverable error; the client must not present stale cached inventory or claim that an offline mutation succeeded.

## Future scope only

If offline access becomes an approved future requirement, first record a new scope/security decision and choose one local store behind the existing repository boundary. Keep persisted models separate from domain entities and map between them. The schema naming, migration, and transaction guidance below applies only if such a store is approved.

## Naming

- Tables/collections: `snake_case`, plural — `orders`, `order_items`.
- Primary key: `id`.
- Foreign keys: `<entity_singular>_id` — `order_id`.
- Timestamps on mutable records: `created_at`, `updated_at`.
- Booleans: prefix `is_` / `has_`.

## Rules

- Store only what the app needs offline — do not mirror the entire backend.
- All DB access goes through the data layer; screens never touch the DB.
- Wrap multi-step writes in a transaction.
- Define each entity with [`_template-entity.md`](./_template-entity.md).

## Migrations

- Every schema change ships a **versioned, forward-only** migration — never mutate an existing one.
- Bump the local schema version on every structural change.
- Test migrations against a **populated** database, not an empty one.
