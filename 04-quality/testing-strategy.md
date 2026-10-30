# Testing Strategy

## Test levels

| Type | Scope | Typical tools/approach | Relative speed |
|---|---|---|---|
| Unit | Domain rules, validators, use cases, facades/state transitions, mappers | Jasmine/Karma or the test runner configured in the Ionic + Angular scaffold; deterministic fakes/mocks | Fast |
| Component/UI | One Ionic page or component in isolation | Angular component tests; verify accessible labels and visible state | Medium |
| API integration | HTTP endpoints, validation, persistence mapping, status/error responses | API tests against a controlled test database or isolated test doubles as appropriate | Medium |
| End-to-end | Main workflows across app/API on a simulator, emulator, or device | Focus first on Android for the academic demo; use additional platform testing if supported and required | Slow |

## MVP behaviors to test

- Expiration boundary conditions using a fixed clock and calendar dates in America/Bogota: yesterday, today, today + 3 days, today + 4 days, and no date.
- Quantity rules: reject zero/negative values; integer quantities for Units/Packages; decimal quantities for weight/volume units.
- Name validation after trimming; invalid unit/category; valid and invalid date-only strings.
- Successful GET with products, successful GET with an empty array, network failure, malformed payload, and server error.
- Create/update only show success after server success; request failure preserves form values; failed delete keeps the item visible.
- Edit route fetches the item by ID, and unknown IDs produce a not-found state.
- Loading, loaded, empty, load error, validation error, saving, save error, delete confirm/failure, and optional search no-results UI states.
- Default ordering: earliest expiration date first; null dates last; ordering remains deterministic for equal dates.

## Rules

- Test behavior and state transitions, not private implementation details.
- Use fixed clocks and deterministic data; do not use real network or sleeps in unit tests.
- Keep domain rules pure and independently testable; mock only direct external dependencies.
- Add regression tests for each discovered defect.
- Target at least 80% coverage in domain rules/use cases and state facades if the chosen test tooling supports reliable coverage measurement; coverage is a floor, not a substitute for meaningful behavior checks.

## Delivery evidence

The implementation now exists on the feature review branches. Local evidence collected on 2026-10-29:

- Mobile app: `npm test -- --configuration ci` — 22 tests passed across six files; `npm run lint` passed; web build passed.
- Android: development build and Capacitor sync passed; Gradle `assembleDebug` succeeded; APK installed and opened on the Pixel 10 Pro Android 16 / API 36.1 emulator.
- API: `npm test` — 9 HTTP tests passed; `npm run build` passed.
- Local integration: Docker Compose reported the API and PostgreSQL containers healthy; Android loaded four seeded products; a test product was created, edited, and deleted through the UI and verified via the API.
- Hosted CI: GitHub reported an account billing lock before any job step started. Those jobs are blocked, not passing and not evidence of a source-code test failure.

The 22-test mobile result is a baseline for the app revision tested on 2026-10-29. The later feature-branch head introduces Haptics/Status Bar ports and adapters, adds native port tests, and standardizes the test command as `npm run test:ci`; these new changes must be revalidated locally before the current head is reported as passing. GitHub Actions is blocked before job steps due an account billing issue, so it provides no pass/fail evidence for the code.

This evidence does not prove that every scenario above has passed. Still pending or requiring explicit retained evidence are the successful empty-pantry UI, all date-boundary cases on the emulator, validation and error-recovery paths on device, haptic/keyboard/back-navigation checks, PostgreSQL integration tests automated in the API suite, dependency vulnerability triage, release signing, and any cloud/store deployment. Additionally, the H3 permission-sensitive native-plugin criterion remains a rubric gap because the accepted MVP defers camera, geolocation, and notifications; Haptics and Status Bar need no runtime permissions. Record commands, date, result, emulator/API level, and the matching output or screenshot. Never present an acceptance criterion as a test result without executing it.
