# Activity 3 — Mobile Build and Deployment Record

- **Date:** 2026-10-29
- **Owner:** Gustavo Gallego
- **Status:** Working record — implementation and release evidence are in progress
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

## Verified baseline evidence

These results describe the scaffold build run before the new MVP implementation was added to its review branch. Re-run these commands against the completed implementation branch before treating them as final release evidence.

| Check | Result | Evidence interpretation |
|---|---|---|
| npm ci | Completed | Dependencies were installed; npm reported six audit findings and install-script approval warnings. |
| npm run build | Passed | Angular generated the web bundle in www. |
| npm run lint | Passed | ESLint reported that all current files passed. |
| npm test -- --configuration ci | 2 tests passed | Only the two starter scaffold tests existed at that time; this was not full PantryPal coverage. |
| npx cap add android | Passed | Capacitor generated a local Android project and copied web assets. |
| npx cap sync android | Passed | Web assets and four installed Capacitor plugins were copied/synchronized. |
| .\android\gradlew.bat -p android assembleDebug | BUILD SUCCESSFUL | Generated a debug APK. Gradle downloaded its wrapper distribution and Android Build Tools 35. |
| Debug APK | 4,315,010 bytes | Located at android/app/build/outputs/apk/debug/app-debug.apk on the workstation. |
| ADB install | Success | The APK installed on emulator emulator-5554. |
| First installed screen | Ionic starter “Blank” | This proved installation and process startup only. It also exposed that product functionality had not yet been implemented in the local scaffold at that point. |

The new implementation branch adds the MVP product domain, API adapter, product list and form, error handling, DTO validation/mapping, and domain tests. It also configures native status-bar text for the light application canvas and invokes best-effort light haptic feedback after server-confirmed create, update, and delete operations. These native APIs must be exercised on the emulator/device; they do not replace server confirmation. The branch still requires local installation, lint, tests, web build, Android rebuild, and device verification before it can be described as passing.

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
- Generate a dedicated release keystore and keep it outside Git, the build logs, and the deliverable source bundle. Keep passwords and signing variables in a secure local store or protected CI secrets.
- Verify package ID, app version, version code, target SDK, icon, release build output, and signing identity before upload.
- A Play Console internal-testing upload is the preferred simulated/controlled distribution path if the required developer account is available. Otherwise, document the store submission as simulated and include real build/signing evidence without claiming a store release.

### iOS

The current development workstation is Windows. An IPA release build requires a macOS/Xcode signing environment and Apple signing assets. Do not claim an IPA has been built on this workstation. If no macOS/Xcode or Apple Developer account is available, document the iOS pipeline as a simulation with the required signing/archive steps and state the platform limitation.

### Cloud services

Firebase Hosting is primarily a web/static hosting option and is not, by itself, a replacement for a REST API and PostgreSQL database. AppFlow or another mobile build service can be evaluated if account access and project integration become available. No cloud deployment or store upload is claimed by this record yet. The immediate reproducible deployment target is a localhost-bound Docker Compose stack; public hosting must wait for a security review because the MVP has no authentication.

## Final test strategy

Before release, record results for:

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

## Current completion status

This record is a working deployment log, not the final 8–10 page submitted memory. At this point, a debug APK was built and installed from the Ionic starter scaffold. The new MVP code and local stack must be pulled, tested, rebuilt, and evidenced before a final report can honestly state that the functional application has passed end-to-end verification.


## Review branches and pull requests

All changes are isolated in review branches. They are deliberately left as draft PRs until local validation has been performed. No changes were merged to protected branches.

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
