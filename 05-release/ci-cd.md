# CI/CD

> Status note (2026-10-30): this document describes target release principles, not a fully implemented store-release pipeline. The current review branches contain Mobile CI and API CI workflow files. GitHub Actions jobs are blocked before their steps start because GitHub reports the account locked due to a billing issue. No release workflow, signed store artifact, or cloud deployment is claimed.

## Principles

- Reproducible builds **from CI only** — no store uploads from a developer laptop.
- Separate build flavors/schemes per environment: `dev`, `staging`, `prod`.
- All environment values (base URL, keys, flags) injected at build time — never hardcoded.

## Pipeline stages

1. **Lint + static analysis** — fails on warnings.
2. **Unit + widget tests** — must pass.
3. **Build signed artifacts** — `.aab`/`.apk` (Android), `.ipa` (iOS).
4. **Distribute to internal testers** — Firebase App Distribution / TestFlight.
5. **Pre-release checks** — startup time + app size captured and compared.
6. **Promote to store review** — after sign-off (see `_template-release-checklist.md`).

## Triggers

- PRs run stages 1–2.
- Merges to `main` run stages 1–4 to internal testers.
- Target future behavior only: a `vX.Y.Z` tag could run a full release pipeline and create a GitHub release. A `.github/workflows/release.yml` file is not present on the current review branch, so this automation is not currently configured.

## Environments

| Flavor | Backend | Distribution |
|--------|---------|--------------|
| dev | dev API | debug installs |
| staging | staging API | internal testers |
| prod | prod API | store |
