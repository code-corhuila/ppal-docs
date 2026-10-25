# Changelog
Format: [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) | [SemVer](https://semver.org/)

## [Unreleased]

### Added
- PantryPal ADR-002 MVP scope decision and realistic MoSCoW backlog priorities.
- Project-specific ADR-001 for Ionic + Angular Signals state management and ADR-003 for centralized Angular Router + Ionic navigation.
- Product domain model, REST API contract, prototype state specification, and planned MVP acceptance test matrix.

### Changed
- Aligned governance, architecture, networking, authentication, offline behavior, and testing guidance with the no-auth, online-only MVP.
- Updated repository overview and documentation indexes to keep MVP traceability discoverable.

## [1.0.0] - 2026-08-28

### Added
- Manual-style governance scaffold for mobile apps: numbered sections, each with a README and copy-and-fill templates.
- `00-sdd-guide.md` — methodology and fill-in order.
- `00-governance`: agile, git, DoR/DoD, security rules, and sprint-retro template.
- `01-architecture`: structure, layers and state, navigation, ADRs (with example ADR-001), and feature/screen templates.
- `02-code-and-ui`: coding standards, design system, accessibility, i18n, and component template.
- `03-api-and-data`: networking, authentication, persistence, offline sync, and endpoint/entity templates.
- `04-quality`: testing strategy, performance budgets, mobile security, and test-case template.
- `05-release`: CI/CD, signing and stores, observability, and release-checklist template.
