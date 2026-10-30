# Mobile Security (Runtime Checklist)

Complements [`00-governance/security-rules.md`](../00-governance/security-rules.md) with checks run before each release.

## Current H3 security assessment — 2026-10-30

This assessment records observed status for the local MVP. It is not a production approval.

| Area | Current status | Evidence / remaining action |
|---|---|---|
| Authentication and user isolation | Not implemented by accepted scope | ADR-002 intentionally defines one demo pantry with no identity/ownership. Keep the stack local or in a controlled demonstration; do not publish it as a multi-user service or use sensitive data. |
| Local cache and tokens | Not applicable to current scope | The app has no login tokens and no offline product cache. Do not mark token-storage/logout checks as passed; they are not implemented features. |
| Debug transport | Restricted to debug build | The debug overlay allows local HTTP for the emulator; the main manifest does not enable cleartext traffic. |
| Release transport | No-go / unresolved | `environment.prod.ts` still contains `https://api.pantrypal.invalid/api`. Configure a real controlled HTTPS endpoint and inspect the merged release manifest before release. |
| Native permissions | No camera, location, or notification features in the MVP | No related runtime permissions are requested. If a future approved feature needs permissions, request them just-in-time, handle denial and permanent denial, and add test evidence. |
| Native plugins | Implemented on the review branch; latest-head validation pending | Haptics and Status Bar now use TypeScript ports, Capacitor adapters, composition-root injection tokens, a hand-written Haptics test double, and a Status Bar port stub. Re-run app tests/lint/build locally after pulling the latest branch. These plugins require no runtime permissions; a rubric requiring permission-sensitive plugins remains a separate gap. |
| Signing | Pending | No release keystore or verified signed release APK/AAB is documented yet. Keep signing material outside Git and reports. |
| Dependencies | Pending triage | Local installs reported 6 app audit findings and 4 API findings. Review `npm audit` detail and fix selectively; do not use force-upgrade as a substitute for impact analysis. |
| Hosted CI | Blocked before execution | GitHub displayed an account billing lock before any runner step started. No CI pass is claimed; local checks remain independently documented. |

## Storage

- [ ] Tokens and secrets only in secure storage (Keychain / Keystore), if a future authenticated scope introduces tokens.
- [ ] Sensitive local data encrypted; cleared on logout, if future local persistence/authentication is approved.
- [x] No product cache or credentials are intentionally persisted by the current mobile MVP.

## Transport

- [x] Debug cleartext networking is allowed only through the debug manifest overlay for local testing.
- [ ] Verify the merged release manifest has cleartext disabled before release.
- [ ] Replace the reserved production placeholder with a real HTTPS API deployment before any public release.
- [ ] Certificate pinning is not implemented; revisit if the deployment threat model and API sensitivity justify it.

## Permissions and native capabilities

- [x] No camera, geolocation, notification, or biometric permission is declared for the current scope.
- [ ] If the H3 evaluation requires two permission-sensitive native plugins, resolve that against ADR-002 and the instructor's rubric before claiming completion.
- [ ] For any future permission-bearing plugin: request only at point of use, explain the reason, handle denied/permanently denied states, and retain emulator evidence.

## Build and release

- [x] Debug-only configuration and local SDK path are excluded from version control.
- [ ] Produce release APK and AAB using a release keystore stored outside Git.
- [ ] Verify APK/AAB signatures using Android build tools/JDK and retain command output.
- [ ] Confirm release build does not contain debug cleartext permissions or the local development API URL.
- [ ] Review icon, app name, versionName/versionCode, min/target SDK, and manifest merger report.
- [ ] Do not upload to a store until the release decision is GO and the API has a real HTTPS endpoint.

## Dependencies

- [ ] Review app and API audit findings, prioritize critical/high issues, and document accepted residual risk.
- [ ] Review third-party plugin versions and their data/permission requirements.
- [ ] Re-run local tests, lint, web build, and Android release checks after dependency changes.
