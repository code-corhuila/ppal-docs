# PantryPal Prototype State Specification

- **Date:** 2026-10-26
- **Owner:** Gustavo Gallego
- **Scope basis:** ADR-002 — MVP Scope
- **Navigation basis:** ADR-003 — Navigation Model
- **Status:** Visual QA completed for the main screen, the original Page 1 delete dialog, and the 11 editable variants on Page 2 on 2026-10-26. Final course-evidence screenshot exports and explicit numeric accessibility contrast checks remain separate follow-up items.

## Purpose

The prototype communicates the main user flow and the most important success/failure states using the existing PantryPal visual language and English UI copy. Do not add login, camera, notification, or offline-operation screens because those are outside the accepted MVP.

**Figma file:** https://www.figma.com/design/ktt2ItT0fUgjI7RBmXHcdc/Design-PantryPal-UI?node-id=20-2

## Editable frame status

The Figma file contains a page named **MVP State Variants** (page ID `20:2`) with these 11 editable frames, arranged in numeric order across two rows:

| Priority | Frame in Figma | Node ID | Coverage |
|---|---|---|---|
| Must | `01 Pantry — Loading` | `23:2` | Loading indicator and message; no premature Add Product action |
| Must | `02 Pantry — Loaded` | `38:2` | Correct sample data/order, expiration labels, and Add Product action |
| Should | `03 Pantry — Empty` | `23:88` | Empty message and one Add your first product action |
| Must | `04 Pantry — Load Error` | `23:175` | Connection error with one Try Again action; no false empty state |
| Must | `05 Product Form — Edit` | `23:349` | Prefilled form and Save Changes |
| Must | `06 Product Form — Validation Error` | `23:418` | Field-level product-name and quantity errors |
| Must | `07 Product Form — Saving` | `23:489` | Saving feedback and disabled-looking primary action |
| Must | `08 Product Form — Save Failed` | `23:558` | Preserved form values and Retry Save |
| Must | `09 Delete — Confirm` | `23:627` | Confirmation for the sample product Greek Yogurt |
| Must | `10 Delete — Failed` | `23:731` | Product remains in pantry; Try Again / Close |
| Should | `11 Pantry — Search No Results` | `23:262` | Search query with no matches and Clear search action |

The original **Page 1** also contains `PantryPal main screen` (node `2:256`), `PantryPal Product Form` (node `4:3`, Add mode), and `PantryPal Delete Confirmation` (node `4:97`). The original main screen and delete-confirmation background have now been aligned with the same date, sample product details and ordering as the Page 2 variants; status-filter controls were removed because US-06 is Could priority.

## Visual QA completed on 2026-10-26

The live Figma screenshots were inspected after edits. The following changes were applied and verified:

- All pantry state headers use `As of Oct 26, 2026 · Soon: Oct 26–29`.
- Dated sample products are ordered: Greek Yogurt (expired Oct 23), Whole Milk (expires Oct 27), Apples (expires Nov 10), then Rice (no expiration date).
- Both the original Page 1 and Page 2 delete-confirmation backgrounds have no deferred status filters.
- Delete dialogs show **Greek Yogurt** as the visible prototype example, rather than displaying a raw `{productName}` token. In the implemented application, the name must still be interpolated from the actual selected product; it must not be hard-coded.
- `10 Delete — Failed` uses `Add Product` on the background and `Try Again` / `Close` in the dialog.
- `04 Pantry — Load Error` uses one `Try Again` action and no global Add Product CTA.
- Loading has no premature Add Product action; the empty state has one Add your first product action, without a duplicate footer CTA.
- The quantity validation message was shortened to `Must be > 0.` to fit its field column.
- The overall page screenshot was reviewed for layout consistency; the original main screen, the original delete dialog, the loading error, and both Page 2 delete dialogs were also inspected individually.

## Expected behavior and acceptance mapping

| Priority | Behavior/state | User-visible behavior | Related stories |
|---|---|---|---|
| Must | Pantry — Loading | Show a loading indicator and “Loading products…” while the request is pending; do not offer list actions before load completes | US-01, US-10 |
| Must | Pantry — Loaded | Show name, category, quantity/unit, expiration date when present, and status; dated products ascend by date; undated items are last | US-01, US-07, US-08 |
| Should | Pantry — Empty | After a successful empty API response, show “Your pantry is empty” and one Add Product action | US-01, US-11 |
| Must | Pantry — Load Error | Show a concise connection/load error and one “Try Again” action; do not pretend the pantry is empty | US-10 |
| Must | Product Form — Add/Edit | Reuse one form; edit mode is prefilled and can clear the optional date | US-02, US-03 |
| Must | Product Form — Validation Error | Show field errors for blank name and invalid quantity; implementation must also validate unit, category, date, and integer-only quantities | US-09 |
| Must | Product Form — Saving | Show “Saving…” and prevent duplicate submission during the request | US-02, US-03 |
| Must | Product Form — Save Failed | Preserve entered values and allow retry; do not show success or navigate away | US-10 |
| Must | Delete — Confirm | Show the selected product name (Greek Yogurt in this static mockup); implementation binds the actual selected product name dynamically. Cancel makes no change | US-04 |
| Must | Delete — Failed | Keep the product visible after failed DELETE; offer Try Again / Close feedback | US-04, US-10 |
| Should | Pantry — Search No Results | Show “No products found” and Clear search; distinguish from an empty pantry | US-05, US-12 |

US-06 — Filter by expiration status remains **Could** priority. The filter controls were removed from the primary loaded-state screen and both delete-dialog backgrounds; do not reintroduce them as committed MVP functionality unless scope changes explicitly.

## Transition and behavior rules

- App start → Pantry — Loading → Pantry — Loaded, Pantry — Empty, or Pantry — Load Error.
- Add opens the form in Add mode; Edit opens the same form in Edit mode with the selected product prefilled.
- Client validation errors appear without a request. API errors after a failed request preserve form values.
- A successful create/update returns to the pantry list only after API confirmation.
- Delete opens a confirmation for the selected product. Cancel changes nothing. Successful API deletion removes the row; failure keeps it visible.
- Search and no-results feedback are Should priority. There is no status-filter control in the accepted MVP baseline.
- Expiration status compares calendar dates in `America/Bogota`: Expired before today; Expiring Soon from today through today + 3 days inclusive; Not Expiring Soon after that; No Expiration Date if absent.

## Accessibility and evidence follow-up

- [x] Loading, empty, no-results, and error states look distinct.
- [x] Primary actions and retry labels were checked for consistency in the screenshots.
- [x] Expiration statuses use readable text labels, not color alone.
- [x] The old Page 1 delete-confirmation background was aligned and its status filters removed.
- [x] The delete mockup uses a real sample name, with the requirement that implementation binds the selected name dynamically documented.
- [ ] Measure and record actual text/background contrast ratios against the project's WCAG AA targets; these numeric checks were not performed in this visual pass.
- [ ] Verify text scaling and screen-reader/focus behavior on the target device or emulator.
- [ ] Export/capture the final screens if additional image evidence is required by the course submission.

The frames illustrate intended UX only; they do not establish that the API or mobile features have been implemented or tested.
