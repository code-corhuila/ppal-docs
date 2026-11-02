# Definition of Done (DoD)

A user story is **done only when all of these are true**:

- [ ] Acceptance criteria met and verified.
- [ ] Code merged to `main` via a reviewed, approved PR.
- [ ] Relevant unit and component/UI tests are added and passing (see [testing-strategy.md](../04-quality/testing-strategy.md)).
- [ ] Runs without regression on the target platform(s) explicitly declared for the activity. Verify additional platforms only when they are in the agreed scope.
- [ ] No new linter, type, or accessibility warnings introduced.
- [ ] Loading, empty, validation, error, and retry states are handled where applicable. Offline operation or synchronization must not be implied unless explicitly included in the accepted scope.
- [ ] User-facing strings follow the agreed language/localization requirements; no accidental hardcoded strings.
- [ ] Analytics / crash reporting events are added only where relevant to the agreed scope and environment.
- [ ] Documentation updated (feature spec and ADR where an architectural decision was made).

A story that does not meet the applicable DoD is **not done** — it stays open. Partial work is not closed "to be finished later". Do not mark stories complete based on a prototype or a specification alone.
