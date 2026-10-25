# ADRs — Architecture Decision Records

ADRs document important architectural decisions. Each file = one decision.

## How to create an ADR

1. Copy _template-adr.md.
2. Name it records/ADR-NNN-short-title.md (e.g. ADR-003-navigation-model.md).
3. Fill it in completely — especially the evaluated alternatives.
4. Once accepted, the status is permanent (it is not deleted; it is "Superseded" by another ADR).

## Possible statuses

- Proposed — under discussion
- Accepted — approved by the project owner/team for the current baseline; still subject to the repository's PR review process
- Rejected — evaluated and discarded (document why)
- Superseded — replaced by ADR-NNN (indicate which)

## ADR register

| # | Title | Status | Date |
|---|-------|--------|------|
| [ADR-001](./records/ADR-001-state-management-pattern.md) | State management pattern (Ionic + Angular Signals) | Accepted | 2026-10-25 |
| [ADR-002](./records/ADR-002-mvp-scope.md) | Minimum Viable Product scope | Accepted | 2026-10-25 |
| [ADR-003](./records/ADR-003-navigation-model.md) | Navigation model (Angular Router + Ionic) | Accepted | 2026-10-25 |

> Add a row here as you create ADRs in records/. The authoritative merge to main still requires the approved pull request.
