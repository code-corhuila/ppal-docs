# Definition of Ready (DoR)

A user story may enter a sprint **only when all of these are true**:

- [ ] The user value is written (As / I want / So that).
- [ ] Acceptance criteria are written and verifiable (Given / When / Then).
- [ ] A UI reference (design or mockup) is linked, if the story touches the UI.
- [ ] The API contract is available or stubbed, if the story calls a backend.
- [ ] Loading, empty, validation, and error/no-network states are described as applicable. Offline behavior is explicitly included or excluded according to the accepted scope; it is never assumed by default.
- [ ] Dependencies on other stories are identified.
- [ ] The story is estimated by the team.
- [ ] The story fits in one sprint (split it if it does not).

If any box is unchecked, the story stays in refinement — it does not enter a sprint. For PantryPal's MVP, consult ADR-002 and the Product Backlog Priorities register before promising any deferred Should/Could behavior.
