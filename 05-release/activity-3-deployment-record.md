# Activity 3 — Mobile Build and Deployment Record

- **Date:** 2026-10-30
- **Owner:** Gustavo Gallego
- **Status:** Local MVP build and CRUD verified; signed release artifacts and selected H3 quality gates remain pending
- **Scope basis:** PantryPal MVP decisions proposed in [ppal-docs PR #1](https://github.com/code-corhuila/ppal-docs/pull/1)
- **Language:** English for all project artifacts, code, comments, commit messages, and user-facing application copy.

## Purpose

This record tracks reproducible evidence for Activity 3: native build preparation, final testing, signing, distribution, cloud/deployment configuration, and post-release maintenance. It distinguishes verified workstation results from planned or not-yet-executed steps. A successful scaffold build is not evidence that all PantryPal product features are complete.

## Target architecture

The intended local demonstration flow is:

1. Ionic + Angular + Capacitor mobile client.
2. Node.js + Express + TypeScript REST API.
3. PostgreSQL, accessed by the API only.
4. Docker Compose for local orchestration of the API and database.

The API and database repositories are separate code repositories, not separate microservices per feature. The mobile app must not connect directly to PostgreSQL. The accepted MVP is a single demonstration pantry, online-only, with no accounts/login, camera, notifications/reminders, or offline cache. This unauthenticated design is restricted to local development or a controlled demonstration; it is not suitable for a public multi-user production service.
The editable local deployment diagram is available at [activity-3-deployment-architecture.mmd](./activity-3-deployment-architecture.mmd). It depicts the actual local API/database boundary and the emulator-to-host route; it does not imply that a public cloud deployment has already occurred.


## Workstation and Android preparation

The following values were collected from the Windows workstation on 2026-10-29:

| Item | Observed value |
|---|---|
| Operating system | Windows 10 build 26300 |
| Node.js | v24.21.0 |
| npm | 11.19.0 |
| Git | 2.55.0.windows.5 |
| Java | Eclipse Temurin OpenJDK 21.0.13 LTS |
| Android Studio | 262.10968.63.0-AI |
| Android SDK | Under the standard per-user Android SDK directory |
| Android platform packages present during inspection | API 34 and API 36 |
| Emulator used for successful run | Pixel 10 Pro, Android 16 / API 36.1, x86_64 |

The initial API 37 virtual device failed because its configured Play Store system-image directory was incomplete. A new Pixel 10 Pro virtual device using API 36.1 was created and started successfully. ADB reported the device state as device.

## Verified implementation evidence

The following results were observed on the Windows workstation on 2026-10-29/30. They are local results on review branches; they are not GitHub Actions results and do not imply that the PRs are merged.

| Check | Result | Evidence interpretation |
|---|---|---|
| Mobile unit tests | `npm test -- --configuration ci`: 22 tests passed across 6 files | Local unit-test evidence only; this does not establish the 80% coverage target. |
| Mobile lint | `npm run lint`: passed | ESLint reported all files pass. |
| Mobile web build | `npm run build`: passed | Angular generated web assets in `www`. |
| Capacitor sync | `npm run build:android:debug`: passed | Web assets and 4 Capacitor plugins synchronized. |
| Android debug build | Gradle `assembleDebug`: `BUILD SUCCESSFUL` | Debug APK generated after setting the local SDK path in `android/local.properties`. |
| Android install | ADB `install -r`: `Success` | Debug APK installed on `emulator-5554`, Pixel 10 Pro, Android 16 / API 36.1. |
| Mobile-to-API read | Passed manually | PantryPal loaded four seeded products from the PostgreSQL-backed REST API. |
| Mobile create/update/delete | Passed manually | A product was created through the app, its quantity changed from 1 to 2, then it was deleted. The API confirmed the values and the absent ID. |
| API automated tests | `npm test`: 9 tests passed | Current HTTP tests use an in-memory repository; they are not automated PostgreSQL integration tests. |
| API TypeScript build | `npm run build`: passed | API source compiled successfully. |
| API date-only contract | Fixed and smoke-tested | PostgreSQL `DATE` now serializes as `YYYY-MM-DD` or `null`, matching the mobile mapper and resolving the observed load failure. |
| Docker Compose | PostgreSQL and API reported healthy | PostgreSQL 17 and the API run locally. API host port is bound to `127.0.0.1:3000`; PostgreSQL is not published to the host. |
| GitHub Actions | Blocked before steps | GitHub reports: “The job has not started because your account is locked due to a billing issue.” The observed jobs have no runner and no executed steps. Do not report them as passing or as code-test failures. |
| Dependency audit | Open follow-up | App install reported 6 audit findings; API install reported 4 (1 moderate, 1 high, 2 critical). Findings need triage. No `npm audit fix --force` was run. |
| API install reproducibility | Open follow-up | The API repository has no committed `package-lock.json`; npm install may resolve different transitive versions later. Add and review a lockfile before treating dependency resolution as reproducible. |

The Android debug build enables local cleartext networking only through `android/app/src/debug/AndroidManifest.xml`. The production environment still contains the reserved placeholder URL `https://api.pantrypal.invalid/api`; no public HTTPS API has been deployed. This debug artifact is suitable for controlled local testing, not public release.

### Native capability status and scope gap

The application currently uses Capacitor Haptics for best-effort tactile feedback after server-confirmed mutations and Capacitor Status Bar for native status-bar styling. These non-sensitive UI capabilities do not request camera, location, or notification permissions. The current review branch now defines pure TypeScript ports for Haptics and Status Bar, Capacitor adapters, root injection-token registration, and tests using a hand-written Haptics double plus a Status Bar port stub. These changes were committed after the 22-test local baseline, so the full app test/lint/build and Android build must be rerun locally after pulling the latest branch before this refactor can be called verified. The port/adapters criterion is now implemented in code but its newest test results are pending. The selected plugins do not request runtime permissions, so this does not satisfy a rubric interpretation that specifically requires a permission-sensitive camera/location plugin and JIT permission-denial tests.

Camera/photos, geolocation, notifications/reminders, login, and offline sync remain excluded by ADR-002. This is an intentional scope/security decision, not a claim that those features were implemented. If the H3 rubric interprets the two-plugin criterion as requiring permission-sensitive plugins specifically, the current MVP scope conflicts with that interpretation; resolve it explicitly with the instructor or through an approved scope amendment. Do not silently mark the permission-flow criterion complete.

## Reproducible local workflow

Use the project branches containing the reviewed work. The repositories must be checked out as sibling directories for the Compose file paths to resolve:

~~~text
pantrypal-workspace/
├── ppal-app/
├── ppal-api/
├── ppal-db/
└── ppal-infra/
~~~

Recommended verification sequence:

1. Install the app dependencies with npm ci in ppal-app.
2. Start the local API and PostgreSQL stack from ppal-infra using Docker Compose.
3. Verify http://localhost:3000/health and http://localhost:3000/api/products from the Windows host.
4. Run app lint and automated tests; save the actual terminal output.
5. Run the development Android build and Capacitor sync.
6. Open the Android project in Android Studio, install on the Pixel 10 Pro AVD, and test list, add, edit, delete, validation, expiration boundaries, loading, empty state, and API failure/retry.
7. Capture dated screenshots or a short recording of the actual run. Do not use static Figma frames as proof of execution.

The local Android emulator reaches the host API through 10.0.2.2:3000. If local HTTP is required, permit cleartext traffic through a debug-only manifest overlay; do not enable it in the main or release manifest. Use a controlled HTTPS endpoint before any production-oriented release build.

## Distribution and signing strategy

### Android

- Use Android Studio/Gradle to produce a debug APK for local tests.
- Produce a release AAB only after the product tests pass and the release API URL is configured to a real HTTPS service.
- The app review branch now includes Gradle logic that reads the ignored local file `android/keystore.properties` and a safe `android/keystore.properties.example` field-name template. The keystore and real passwords have not been created or committed.
- Generate a dedicated release keystore locally and keep it outside Git, the build logs, and the deliverable source bundle. Keep passwords and signing variables in a secure local store or protected CI secrets.
- Without that local properties file, Gradle may produce an unsigned release artifact; signature verification remains a required gate.
- Verify package ID, app version, version code, target SDK, icon, release build output, and signing identity before upload.
- A Play Console internal-testing upload is the preferred simulated/controlled distribution path if the required developer account is available. Otherwise, document the store submission as simulated and include real build/signing evidence without claiming a store release.

### iOS

The current development workstation is Windows. An IPA release build requires a macOS/Xcode signing environment and Apple signing assets. Do not claim an IPA has been built on this workstation. If no macOS/Xcode or Apple Developer account is available, document the iOS pipeline as a simulation with the required signing/archive steps and state the platform limitation.

### Cloud services

Firebase Hosting is primarily a web/static hosting option and is not, by itself, a replacement for a REST API and PostgreSQL database. AppFlow or another mobile build service can be evaluated if account access and project integration become available. No cloud deployment or store upload is claimed by this record yet. The immediate reproducible deployment target is a localhost-bound Docker Compose stack; public hosting must wait for a security review because the MVP has no authentication.

## Final test strategy

The list/create/update/delete path is verified locally, but it is not the full acceptance matrix. Before a release decision, record results for:

- Domain unit tests: expiry before today, today, today plus three days, today plus four days, and no expiration date in the America/Bogota calendar.
- Validation: blank name, quantity <= 0, fractional quantity for Units/Packages, unsupported unit/category, invalid calendar date, valid optional null expiration date.
- API behavior: list, get-by-ID, create, update, delete, validation errors, not found, and network/server failures.
- UI behavior: loading, successfully empty pantry, loaded inventory, no-results search, validation errors, saving, save failure with values retained, delete confirmation, and delete failure without false success.
- Native execution: startup on an emulator, status-bar appearance, haptic feedback after server-confirmed mutations, form keyboard behavior, Android back navigation, and API connectivity.
- Release checks: signing, artifact identity, and no secrets in Git history or artifacts.

Only enter results after running the tests. For each evidence item, retain date, command, device/API level, result, and the corresponding screenshot or output.

## Post-deployment maintenance

- Keep Node, Angular, Ionic, Capacitor, Gradle, Android SDK, and dependencies updated through reviewed pull requests.
- Review audit findings and install-script warnings; do not use a force-upgrade as a substitute for understanding dependency impact.
- Preserve database backups before schema changes and test restore procedures before any persistent hosted deployment.
- Monitor API health, response errors, crashes, and latency only in a controlled environment using a documented retention policy.
- Keep API credentials, release signing material, and environment-specific configuration outside the repository.
- Revisit authentication, multi-user isolation, HTTPS hosting, offline behavior, native reminders, and camera features only through an explicit scope/security decision.

## Current completion status and Go / No-Go

**GO for a controlled local academic demonstration after re-validation of the latest branch:** the prior app revision was built, installed, and manually verified against the local PostgreSQL-backed API for list/create/update/delete. The prior revision passed 22 mobile tests, lint, web build, API tests (9), API TypeScript build, and Android debug Gradle build. Since then, Haptics/Status Bar were refactored behind ports and new tests were added; re-run the mobile checks against the latest head before treating the latest revision as verified.

**NO-GO for public or store release:** no real HTTPS production API exists; the production URL is still a reserved placeholder; the app/API have no authentication or per-user isolation; dependency findings remain untriaged; no release keystore was created; no release AAB/APK has been signed and verified; no cloud deployment or store upload occurred; GitHub Actions is blocked before its steps start; test coverage by folder and the complete H3 native-plugin-port criterion are not evidenced. The debug configuration must never be used for production.

This distinction must be preserved in the Activity 3 memo. A local debug APK and a working CRUD path are valid evidence, but they are not a signed release. Update this record again when release artifacts, port/adapter tests, dependency review, and remaining acceptance cases are completed.

## Review branches and pull requests

All changes are isolated in review branches and none have been merged to protected branches. The app, API, DB, infrastructure, and Activity 3 documentation PRs remain drafts while remaining release evidence is prepared. The H2 documentation PR is open for instructor review; merge only after the required approval.

| Repository | Branch | Draft pull request | Scope |
|---|---|---|---|
| ppal-db | feat/mvp-product-schema | https://github.com/code-corhuila/ppal-db/pull/1 | Product table migration and demo seed |
| ppal-api | feat/mvp-products-api | https://github.com/code-corhuila/ppal-api/pull/1 | Validated REST endpoints and HTTP tests |
| ppal-infra | feat/local-mvp-stack | https://github.com/code-corhuila/ppal-infra/pull/1 | Local PostgreSQL/API Docker Compose stack |
| ppal-app | feat/h3-pantrypal-mvp | https://github.com/code-corhuila/ppal-app/pull/14 | Mobile MVP flow, validation, HTTP adapter, native integration, unit tests |
| ppal-portal | chore/english-readme | https://github.com/code-corhuila/ppal-portal/pull/1 | English documentation; optional portal remains out of scope |
| ppal-tools | chore/english-readme | https://github.com/code-corhuila/ppal-tools/pull/1 | English tooling documentation |
| ppal-docs | docs/h3-deployment-plan | https://github.com/code-corhuila/ppal-docs/pull/2 | Activity 3 deployment and evidence record |

GitHub Actions runs were created for the app/API PRs, but the observed jobs had no assigned runner and exposed no executed steps. They are not passing evidence. Local Windows verification remains mandatory before these drafts should be marked ready for review.


---

## Verification update and course-rubric traceability — 2026-11-02

This addendum supersedes older baseline counts in this record where newer local evidence is listed below. All results are from the developer's Windows workstation and Android emulator; they are not GitHub Actions results.

### Latest mobile test and coverage evidence

| Check | Latest observed result | Interpretation |
|---|---|---|
| Current runtime source baseline | App runtime on `develop` commit `1938f34`; test/coverage changes are isolated in PR #19 and do not modify runtime features | The debug APK below is a debug artifact from the baseline runtime revision; it is not a signed release build from the coverage branch |
| Mobile unit tests with coverage | 41 passed across 7 files | Local execution completed successfully |
| Domain layer coverage | 100% statements, 90.76% branches, 100% functions, 100% lines | Exceeds 80% for each listed metric except branch coverage is also above 80% |
| Pantry presentation/ViewModel coverage | 100% statements, branches, functions, and lines | Exceeds the scoped coverage target for the facade/ViewModel |
| Whole-app coverage | 55.29% statements, 82.69% branches, 58.33% functions, 63.36% lines | The whole-app result is below 80%; HomePage HTML/TypeScript is the main uncovered area. Do not describe the whole app as having 80% coverage. |
| Mobile lint | `npm run lint`: “All files pass linting.” | Passed locally |
| Web build | `npm run build`: application bundle generation complete; initial total 972.80 kB (191.09 kB estimated transfer) | Passed locally |
| Capacitor debug sync | `npm run build:android:debug` completed web bundle generation and sync; four Capacitor plugins were listed | Passed locally |
| Android debug build | Gradle `assembleDebug`: `BUILD SUCCESSFUL in 20s`; 213 actionable tasks, 159 executed and 54 up-to-date | Passed locally; output is a debug build |
| Debug APK | `android/app/build/outputs/apk/debug/app-debug.apk`; 6,031,866 bytes | Installed successfully on the Pixel 10 Pro AVD, Android emulator device `emulator-5554` |
| APK SHA-256 | `4DE28B04815C9ED90B96ECA3901FEEFF55DCE2E65414A1D9AD60EB190AC366B3` | Identifies the local debug APK copied to the evidence folder |
| Manual CRUD | A demo product was created, updated from quantity 2 to 3, and deleted; the four seeded products remained | Demonstrated in user-provided emulator screenshots |
| API outage/recovery | API was stopped while PostgreSQL stayed healthy; the app showed its connection-error/Retry state. API was restarted, `/health` returned `{"status":"ok"}`, and the four products reloaded | Demonstrated in user-provided emulator screenshots and terminal output |
| Coverage dependency | `@vitest/coverage-v8@4.1.12` added to package devDependencies on `chore/add-vitest-coverage-provider` | Open PR #19; not yet merged into `develop` |
| Latest app PR/merge status | Coverage tests and provider are proposed by [ppal-app PR #19](https://github.com/code-corhuila/ppal-app/pull/19) | Open review PR; do not claim the changes are merged until GitHub reports a merge |
| Dependency audit | Current mobile dependency install reports 25 findings: 1 low, 23 moderate, 1 high | Open security-maintenance item; do not use `npm audit fix --force` without reviewing breaking impacts |

### What the debug APK will and will not do on a physical phone

The Android debug APK proves that the mobile client can be packaged, installed, and opened. It does not contain the REST API or PostgreSQL database. This is an intentional separation of responsibilities: the mobile client communicates with the API, and only the API accesses PostgreSQL.

The local emulator configuration uses the development URL `http://10.0.2.2:3000/api`. The address `10.0.2.2` is the Android emulator's special alias for the host computer; it is not a general host address on a physical phone. In addition, Docker Compose binds the API host port to `127.0.0.1:3000`, so another device cannot reach that service over the LAN. Consequently, the current debug APK can open on a real phone but its product requests are not expected to work from that phone using the current backend configuration. It should show the API failure/retry state instead.

This is not because the database must be installed inside the phone. The required API and database must be running at a network address reachable by the phone. To make a physical-device demonstration work, either deploy the API/database in a controlled environment and configure a real HTTPS API origin, or intentionally configure a secured LAN-only test endpoint and appropriate network binding for a temporary test. The first option is safer and more representative of a production architecture. Neither a public API deployment nor a real production HTTPS endpoint exists at the time of this addendum. The configured production URL remains the reserved placeholder `https://api.pantrypal.invalid/api`; do not present it as deployable.

### H3 checklist: satisfied criteria and explicit gaps

| Checklist item from the instructor's class slides | Evidence / rationale | Status |
|---|---|---|
| Ionic, Angular, and Capacitor running on Android | Debug APK built, installed, and executed on the Android emulator | Demonstrated |
| Presentation, domain, and data layers by feature | Feature-first implementation with domain validation/date rules, repository contract/HTTP mapper, and presentation facade | Implemented |
| State-pattern ADR and at least three states | ADR-001 documents the state approach; loading, loaded, and error states are present; the API outage/recovery test was demonstrated | Implemented and demonstrated |
| Central route registry and guard | ADR-003 documents route table, edit resolver, and unsaved-change guard. No auth guard is required because there are no account/protected routes in this MVP. | Implemented with scope rationale |
| HTTP client, interceptors, token refresh | Configured HTTP client/error handling and bounded read retry exist. There are no access/refresh tokens because login/accounts are excluded. Therefore the “refresh fails → one retry → return to login” behavior is not applicable to this release and is not claimed as implemented. If authentication enters scope, it needs a new approved ADR and tests. | Partially applicable; token flow not implemented |
| Read cache demonstrated in airplane mode | The app does not cache pantry products for offline reads. This is intentionally online-only under ADR-002 and the app shows a recoverable error without inventing an empty pantry. The checklist may still count this criterion as unmet. | Not implemented; explicit rubric gap |
| Two native plugins behind ports, with permission flow | Haptics and Status Bar are behind ports/adapters. These plugins do not request runtime camera/location/notification permissions, so they cannot demonstrate JIT permission denial. A strict reading of the instructor's permission-sensitive-plugin criterion remains unmet. | Ports/adapters implemented; permission-sensitive criterion unmet |
| Green tests, signed APK/AAB, checklist | 41 tests passed; scoped domain and presentation coverage exceeded 80%; debug APK built/installed. No release keystore, signed release APK/AAB, signature verification, cloud deployment, store upload, or IPA currently exists. | Tests/build demonstrated; signed release/distribution still pending |

The instructor's class slides state that an item that cannot be verified in the repository, app, or video should not be claimed as fulfilled. Keep the two rubrics gaps (airplane-mode cache and permission-sensitive plugins) visible. Do not silently change ADR-002 to imply these features exist. If the instructor accepts the current scope rationale, retain the explicit deviation; if they require both features as mandatory, obtain approval for an ADR amendment and implement/test them as a bounded, separately reviewed change.

### Repository licensing

The `ppal-docs` default branch already contained an MIT License. At the time of this update, the other six repositories did not have a root `LICENSE` on their default `develop` branch. License-only pull requests were opened to add the same MIT text from `ppal-docs`:

- [ppal-api PR #6](https://github.com/code-corhuila/ppal-api/pull/6)
- [ppal-app PR #20](https://github.com/code-corhuila/ppal-app/pull/20)
- [ppal-db PR #5](https://github.com/code-corhuila/ppal-db/pull/5)
- [ppal-infra PR #5](https://github.com/code-corhuila/ppal-infra/pull/5)
- [ppal-portal PR #4](https://github.com/code-corhuila/ppal-portal/pull/4)
- [ppal-tools PR #4](https://github.com/code-corhuila/ppal-tools/pull/4)

These PRs are open and mergeable, not yet merged. The copyright line has been copied from `ppal-docs/LICENSE`; confirm that the named copyright holder is appropriate for the contributions in each repository before merging. After the license is reviewed into `develop`, promote it to `qa` and `main` through the usual protected-branch process.

### ADR review and improvement plan

ADR-002 has been extended with an H3 criterion-to-evidence table. Its structure is intentional: context and constraints explain why the problem matters, the decision states the scope, alternatives show what was rejected, justification explains the trade-offs, and consequences/reconsideration criteria show when the choice should change. For oral defense, be prepared to explain not only what was chosen but also what a rejected option would cost and what evidence would trigger reopening the decision.

Recommended future ADR quality improvements:
1. Link Must stories to acceptance criteria, source locations, tests, and runtime evidence.
2. Keep implementation evidence separate from the original accepted decision; update dated implementation notes rather than rewriting history.
3. Treat the no-offline-cache and permission-sensitive-plugin checklist items as explicit grading gaps until accepted by the instructor or addressed through an approved scope amendment.
4. Record why token refresh is not applicable while no authentication/token model exists.
