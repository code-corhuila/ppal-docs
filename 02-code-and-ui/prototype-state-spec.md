# PantryPal Prototype State Specification

- **Date:** 2026-10-25
- **Owner:** Gustavo Gallego
- **Scope basis:** ADR-002 — MVP Scope
- **Navigation basis:** ADR-003 — Navigation Model
- **Status:** Main state variants have been created as editable Figma layers. Final visual QA and Activity 1 screenshot capture are still pending.

## Purpose

The prototype communicates both the main user flow and the most important success/failure states. It uses the existing PantryPal visual language and English UI copy. Do not add login, camera, notification, or offline-operation screens because those are outside the accepted MVP.

**Figma file:** https://www.figma.com/design/ktt2ItT0fUgjI7RBmXHcdc/Design-PantryPal-UI?node-id=0-1&t=L9wdZ7Y3pFhuJE3o-1

## Editable frame status

The Figma file now has a dedicated page named **MVP State Variants** (page ID `20:2`) containing these editable frames:

| Priority | Frame in Figma | Node ID | Coverage |
|---|---|---|---|
| Must | `01 Pantry — Loading` | `23:2` | Static loading indicator and loading message |
| Must | `04 Pantry — Load Error` | `23:175` | Connection error with Try again action |
| Should | `03 Pantry — Empty` | `23:88` | Empty pantry message and Add Product action |
| Should | `11 Pantry — Search No Results` | `23:262` | Search query with no matches and Clear search action |
| Must | `05 Product Form — Edit` | `23:349` | Prefilled form and Save Changes |
| Must | `06 Product Form — Validation Error` | `23:418` | Field-level product-name and quantity errors |
| Must | `07 Product Form — Saving` | `23:489` | Saving feedback and disabled-looking primary action |
| Must | `08 Product Form — Save Failed` | `23:558` | Preserved form values and Retry Save |
| Must | `09 Delete — Confirm` | `23:627` | Confirmation with a dynamic `{productName}` placeholder |
| Must | `10 Delete — Failed` | `23:731` | Product remains in pantry; Try Again / Close |

The original Page 1 also contains the **PantryPal main screen** (node `2:256`, the loaded-state baseline), **PantryPal Product Form** (node `4:3`, Add mode), and **PantryPal Delete Confirmation** (node `4:97`). The loaded-state baseline was updated to remove the status-filter controls, update the reference date to October 25, 2026, correct sample product dates, and order products with dated items first and undated Rice last. The Page 1 delete-dialog text now uses `{productName}` rather than hard-coding Whole Milk.

**Review note:** the last screenshot available in this session showed that `09 Delete — Confirm` (node `23:627`) still had a stale background and that `10 Delete — Failed` (node `23:731`) still had `Cancel` as its secondary action. The user subsequently confirmed that the Figma corrections are done and plans a final re-check when the Figma MCP quota resets. Therefore the recorded issues above are historical observations, not a claim that they remain unfixed. Final visual verification and Activity 1 screenshot capture remain pending; confirm those details against the live Figma file before marking the prototype evidence complete.

## Expected behavior and acceptance mapping

| Priority | Behavior/state | User-visible behavior | Related stories |
|---|---|---|---|
| Must | Pantry — Loading | Show a static loading indicator and “Loading products…” while the request is pending | US-01, US-10 |
| Must | Pantry — Loaded | Show product name, category, quantity/unit, expiration date when present, and status; dated products ascend by expiration date; undated items are last | US-01, US-07, US-08 |
| Should | Pantry — Empty | Only a successful empty API response should produce “Your pantry is empty” and an Add Product action | US-01, US-11 |
| Must | Pantry — Load Error | Show a concise connection/load error and “Try again”; do not pretend the pantry is empty | US-10 |
| Must | Product Form — Add/Edit | Reuse a form layout; edit mode is prefilled and allows clearing the optional date | US-02, US-03 |
| Must | Product Form — Validation Error | Show field errors for blank name and invalid quantity; implementation must also validate unit, category, date, and integer-only quantities | US-09 |
| Must | Product Form — Saving | Show “Saving…” and prevent duplicate submission during the request | US-02, US-03 |
| Must | Product Form — Save Failed | Preserve entered values and allow retry; do not show success or navigate away | US-10 |
| Must | Delete — Confirm | Name the selected product dynamically as `{productName}`; Cancel makes no change | US-04 |
| Must | Delete — Failed | Keep the product visible after failed DELETE; offer retry/close feedback | US-04, US-10 |
| Should | Pantry — Search No Results | Show “No products found” and Clear search; distinguish from an empty pantry | US-05, US-12 |

US-06 — Filter by expiration status remains **Could** priority. The filter controls were removed from the primary loaded-state screen; do not reintroduce them as committed MVP functionality unless the scope changes explicitly.

## Transition and behavior rules

- App start → Pantry — Loading → Pantry — Loaded, Pantry — Empty, or Pantry — Load Error.
- Add opens the form in Add mode; Edit opens the same form in Edit mode with the selected product prefilled.
- Client validation errors appear without a request. API errors after a failed request preserve form values.
- A successful create/update returns to the pantry list only after API confirmation.
- Delete opens a confirmation for the selected product. Cancel changes nothing. Successful API deletion removes the row; failure keeps it visible.
- Search and no-results feedback are Should priority. There is no status-filter control in the accepted MVP baseline.
- Expiration status compares calendar dates in `America/Bogota`: Expired before today; Expiring Soon from today through today + 3 days inclusive; Not Expiring Soon after that; No Expiration Date if absent.

## Visual review checklist

The following must be checked manually in Figma before screenshot capture:

- [ ] State messages, labels, buttons, and field errors are not clipped or overlapping.
- [ ] The Page 2 frames retain the visual style of the Page 1 baseline.
- [ ] Retry and primary actions have clear labels and readable contrast.
- [ ] Expiration status is not conveyed by color alone.
- [ ] The older Page 1 delete-confirmation background copy is retired or its deferred filter controls are removed.
- [ ] The delete placeholder `{productName}` is understood as a prototype placeholder to be bound to the actual selected name in implementation.

## Capture checklist for Activity 1

After the visual review, capture at least:
1. Pantry loaded/list.
2. Add product form and edit form.
3. Delete confirmation.
4. Empty pantry.
5. Load/API error with retry.
6. Form validation and save failure.
7. Search no-results state, labeled **Should** priority.

Caption each screenshot with the Figma frame name and the linked story/priority. The frames illustrate intended UX only; they do not establish that API or mobile functionality has been implemented.
