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

The current application repository has only its initial scaffold; product features and their test evidence must not be represented as implemented until they exist and pass. Record each verification command/result separately from proposed acceptance criteria.
