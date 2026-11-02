# Activity 3 — Oral Defense Brief

- **Project:** PantryPal
- **Prepared:** 2026-11-02
- **Purpose:** A concise, evidence-based guide for the five-minute project presentation. It does not replace the technical memo or implementation evidence.

## Suggested five-minute run of show

### Minutes 0:00–0:30 — Problem and scope

PantryPal tracks pantry products and expiration dates. The accepted MVP focuses on one controlled demonstration pantry and online CRUD operations. The API is the source of truth; the mobile client does not access PostgreSQL directly. The narrow scope is deliberate because login, ownership, camera, notifications, and offline persistence each introduce additional security, lifecycle, and testing requirements.

### Minutes 0:30–1:15 — Architecture and ADRs

Show the repository architecture diagram and ADR-002. Explain that an ADR captures context, constraints, the decision, evaluated alternatives, justification, consequences, and reconsideration criteria. This structure makes trade-offs reviewable instead of treating an implementation choice as self-evident. ADR-002 chooses a single-pantry online MVP; ADR-001 describes the state pattern; ADR-003 describes centralized navigation/resolution and the unsaved-changes guard; ADR-004 explains the framework and tooling selection.

### Minutes 1:15–2:00 — Layers and observable states

Show the repository source and the app running. The mobile code separates presentation/facade, domain rules, and data/API mapping. Demonstrate loaded inventory and the API-error/retry state. The API is the only component that reads or writes PostgreSQL.

### Minutes 2:00–3:00 — Live demo

Use the Android emulator and demonstrate, in this order:
1. Load the four seeded products.
2. Create a temporary product.
3. Edit the quantity and save.
4. Delete the temporary product after confirmation.
5. If time allows, show the API failure/recovery screenshots rather than stopping the backend live during the timed demo.

The current debug APK uses the emulator-only development API address. Keep the local PostgreSQL/API stack running for the demo.

### Minutes 3:00–5:00 — Questions

Answer with the actual evidence and explicitly label what is not implemented. Do not describe a planned feature as completed.

## Expected questions and evidence-based answers

### 1. Where are dependency versions guaranteed?

For the mobile client, `package-lock.json` is committed and the dependency installation was performed with `npm ci`, which uses the lockfile. Vitest and the coverage provider are pinned/resolved consistently at 4.1.12 in the coverage branch. The app's local `npm run lint` passed and `npm run build` passed. The API repository still lacks a committed `package-lock.json`; its reproducible dependency resolution is an open gap and should be fixed before a real release. Do not claim every repository has a fully locked dependency graph until the API lockfile is added and reviewed.

### 2. What happens if token refresh fails?

Token refresh is **not a current PantryPal MVP feature**: there are no accounts, login, access tokens, or refresh tokens. The app has a configured HTTP client and bounded handling for selected read requests, but that is not token refresh. If authentication is approved in a future ADR, the design should serialize a refresh attempt, retry the original request at most once, and on refresh failure clear any session credentials and return the user to sign-in. This proposed behavior is not implemented in the current release.

### 3. What happens if a user denies a native permission?

The current adapters use Capacitor Haptics and Status Bar. Neither selected plugin requires a runtime camera, geolocation, or notification permission. Therefore, this implementation cannot demonstrate a permission-denial flow. Camera, location, and notifications were excluded by ADR-002 for scope, privacy, and lifecycle reasons. Under a strict interpretation of the instructor's permission-sensitive-plugin criterion, this item remains unmet. If it is mandatory, ask for approval to amend the scope, select a permission-sensitive capability with real user value, and test grant, denial, retry, and settings-recovery paths. Do not claim the current plugins fulfil that criterion.

### 4. What would change in the next version?

Prioritize based on verified gaps:
1. Deploy the API to a controlled HTTPS endpoint and configure the app for a real device.
2. Add authentication, server-side authorization, product ownership/data isolation, and secure session handling before any public multi-user release.
3. Resolve the course decision about airplane-mode read caching and permission-sensitive native plugins through an approved ADR amendment if required.
4. Increase HomePage UI test coverage; the current scoped domain and pantry presentation/ViewModel coverage exceeds 80%, while whole-app coverage is 55.29% statements.
5. Add an API lockfile, triage dependency advisories, and verify a signed release AAB/APK.
6. Add a cloud/store distribution pipeline and monitoring only when the secure endpoint, signing assets, and account access exist.

## Current verification facts

- Mobile unit tests with v8 coverage: **41 passed across 7 files**.
- Domain layer: **100% statements, 90.76% branches, 100% functions, 100% lines**.
- Pantry presentation/ViewModel: **100% across reported metrics**.
- Whole app: **55.29% statements, 82.69% branches, 58.33% functions, 63.36% lines**.
- `npm run lint`: passed locally.
- `npm run build`: passed locally; initial bundle 972.80 kB, estimated transfer 191.09 kB.
- Android Gradle debug build: `BUILD SUCCESSFUL`; 6,031,866-byte debug APK generated and installed on the Android emulator.
- Manual CRUD and API outage/recovery were demonstrated with real screenshots.
- No signed release APK/AAB, IPA, public HTTPS backend, cloud deployment, or store upload has been evidenced.
- GitHub Actions jobs were blocked before executing steps under the observed billing/runner lock; they are not passing CI evidence.

## Explaining why the APK does not provide a standalone physical-phone demo

The APK contains the hybrid mobile client, not the PostgreSQL database or REST API. This is the intended architecture, not a requirement to install the database inside the phone. The present debug build targets `http://10.0.2.2:3000/api`, a special Android-emulator alias for the host computer. A physical phone cannot use that alias to reach the developer's laptop, and Docker Compose binds the API to `127.0.0.1:3000`. Therefore, the app can open on a phone but its product requests will fail until an API endpoint accessible to that phone is configured. A real release should use a controlled HTTPS API and must not enable release cleartext traffic.

## Evidence to have open before the presentation

- GitHub repositories and the relevant open PRs.
- ADR-001 through ADR-004 and the new H3 rubric-alignment section in ADR-002.
- Android emulator with the current local API/PostgreSQL stack running.
- Screenshots: pantry list, create, update, delete confirmation, delete result, API unavailable, API recovered.
- Terminal results for 41 tests + coverage, lint, web build, Capacitor sync, and Gradle debug build.
- Debug APK artifact and recorded SHA-256.
- This defense brief as a backup if the live demo fails.

## Demo fallback

A live demo can fail because a service, emulator, or network is unavailable. Keep the screen recording or screenshots ready, but label them as previously captured evidence. Explain the current state honestly and do not imply that a screenshot was generated during the presentation.
