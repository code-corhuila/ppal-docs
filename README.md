# PantryPal (ppal)

PantryPal is a hybrid mobile application focused on a single demonstration pantry, product inventory, expiration dates, and reducing food waste. The accepted MVP is intentionally small: no user accounts, no login, and online-only operations.

## Repository workflow

This documentation repository uses `main` as its only long-lived branch. Submit changes through a working branch named `docs/<topic>` and a pull request. Do not commit directly to `main`, force-push to it, or delete it. The instructor's approval is required before a PR is merged.

## Project repositories

| Repository | Role | MVP status |
|---|---|---|
| [`ppal-docs`](https://github.com/code-corhuila/ppal-docs) | Project governance and source of truth for scope, architecture, and contracts | In use |
| [`ppal-app`](https://github.com/code-corhuila/ppal-app) | Ionic + Angular + Capacitor mobile client | MVP target; currently an initial scaffold |
| [`ppal-api`](https://github.com/code-corhuila/ppal-api) | Single REST API and business/data access boundary | MVP target |
| [`ppal-db`](https://github.com/code-corhuila/ppal-db) | PostgreSQL schema and migrations | MVP target |
| [`ppal-infra`](https://github.com/code-corhuila/ppal-infra) | Infrastructure/orchestration configuration | MVP target |
| [`ppal-portal`](https://github.com/code-corhuila/ppal-portal) | Optional web portal | Not in the MVP |
| [`ppal-tools`](https://github.com/code-corhuila/ppal-tools) | Shared development tooling | Supporting repository |

The architecture is split across separate repositories; this is not a monorepo. The mobile app must access PostgreSQL only through the REST API.

## Project decision records and scope

- [ADR-001 — State management pattern](./01-architecture/decisions/records/ADR-001-state-management-pattern.md)
- [ADR-002 — MVP scope](./01-architecture/decisions/records/ADR-002-mvp-scope.md)
- [ADR-003 — Navigation model](./01-architecture/decisions/records/ADR-003-navigation-model.md)
- [ADR-004 — Technology stack selection](./01-architecture/decisions/records/ADR-004-stack-selection.md)
- [Product backlog priorities](./00-governance/product-backlog-priorities.md)
- [Product API contract](./03-api-and-data/product-api-contract.md)
- [Product domain model](./03-api-and-data/product-domain-model.md)
- [Prototype state specification](./02-code-and-ui/prototype-state-spec.md)
- [MVP acceptance test matrix](./04-quality/mvp-acceptance-matrix.md)

The operational backlog is maintained in the GitHub Project `ppal-backlog`. Its cards and priority fields must be kept synchronized with the documented backlog register.

---

## Mobile Governance Framework

This repository contains a project-adapted documentation framework inspired by the [Mobile Governance Framework](https://github.com/jesusarielgb-works/mobile-governance-framework) by [Jesus Ariel Gonzalez Bonilla](https://github.com/jesusarielgb-works). The original framework and templates are adapted here to PantryPal's actual technology stack and accepted MVP scope.

## Documentation sections

| Section | Contents |
|---|---|
| [00 — Governance](./00-governance/) | Agile conventions, backlog priorities, ready/done definitions, Git and security rules |
| [01 — Architecture](./01-architecture/) | Project structure, layers, state management, navigation, ADRs |
| [02 — Code & UI](./02-code-and-ui/) | Coding standards, design system, accessibility, localization, prototype state specification |
| [03 — API & Data](./03-api-and-data/) | Product API contract, domain model, networking, online-only behavior |
| [04 — Quality](./04-quality/) | Testing strategy, performance budgets, mobile security |
| [05 — Release](./05-release/) | CI/CD, signing, stores, observability |

## How to contribute

1. Read the relevant section README and project decision records.
2. Create a topic branch from `main`.
3. Use a Conventional Commit message and submit a PR.
4. Keep implementation claims factual: a specification or prototype is not an implemented feature.
5. Do not merge the documentation PR until the required review and approval are complete.

## License

MIT. See [LICENSE](./LICENSE).
