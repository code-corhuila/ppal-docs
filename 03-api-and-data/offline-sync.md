# Offline & Sync — MVP Scope

## Current decision

PantryPal's MVP uses an **online-only** strategy. It does not promise cached offline reads, offline writes, queued mutations, conflict resolution, or synchronization. This aligns with ADR-002 — MVP Scope.

## Required connectivity behavior

- Treat a failed request or unavailable network as an error, not as an empty pantry.
- Show a clear error and a retry action.
- Do not show create/edit/delete as successful until the REST API confirms the operation.
- Preserve unsaved form values where feasible after a request fails.
- Do not display stale locally cached products because no offline cache is part of the MVP.

## Future strategies (not part of the MVP)

| Strategy | Meaning |
|----------|---------|
| Online-only (chosen) | Data is requested from the API; failure is visible and retryable |
| Read-through cache | Read from a cache offline, but require connectivity to write |
| Offline-first | Support full read/write offline and synchronize on reconnect |

Any move away from online-only requires a scope review and a new or superseding ADR. A future offline implementation would also need an explicit conflict policy, idempotent sync, retry limits/backoff, and UI states for stale/syncing data; none of those are current MVP commitments.
