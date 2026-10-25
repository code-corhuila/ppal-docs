# PantryPal Prototype State Specification

- **Date:** 2026-10-25
- **Owner:** Gustavo Gallego
- **Scope basis:** ADR-002 — MVP Scope
- **Navigation basis:** ADR-003 — Navigation Model
- **Status:** Design specification for updating the existing Figma file; the actual Figma frames must still be created/updated and captured.

## Purpose

The existing prototype establishes the main pantry list, product form, delete-confirmation interaction, and basic connections. It should be expanded to communicate important success and failure states rather than only ideal-path screens. Reuse the existing visual language and frame structure; do not add login, camera, notification, or offline-operation screens because those are outside the accepted MVP.

**Figma file:** https://www.figma.com/design/ktt2ItT0fUgjI7RBmXHcdc/Design-PantryPal-UI?node-id=0-1&t=L9wdZ7Y3pFhuJE3o-1

## Frames to add or update

Use the names below to make the frames easy to map to acceptance criteria. These are design states/variants, not separate routes unless specified.

| Priority | Frame / variant name | What it demonstrates | User-visible behavior | Related stories |
|---|---|---|---|---|
| Must | 01 Pantry — Loading | Initial list request is in progress | Show a spinner or restrained skeleton and “Loading pantry…”; prevent duplicate actions while initial load is unresolved | US-01, US-10 |
| Must | 02 Pantry — Loaded | API returned products | Show product name, category, quantity/unit, expiration date when present, and status; order dated items ascending and undated items last | US-01, US-07, US-08 |
| Should | 03 Pantry — Empty | API returned HTTP success with zero products | Show “Your pantry is empty” and an “Add product” primary action; visually distinct from an error | US-11 |
| Must | 04 Pantry — Load Error | Initial or refresh request failed | Show a concise connection/load error and “Try again”; never display the empty state as if the request succeeded | US-10 |
| Must | 05 Product Form — Add/Edit | Reuse one form for create and edit | Add mode has blank defaults; edit mode is prefilled from the selected product and can clear the optional expiration date | US-02, US-03 |
| Must | 06 Product Form — Validation Error | Submit invalid field values | Show field-level errors for blank name, quantity less than or equal to zero, unsupported unit/category, invalid date, or integer-only quantity violations; focus the first invalid field | US-09 |
| Must | 07 Product Form — Saving | POST/PUT is in progress | Show “Saving…” on the primary action and prevent duplicate submission while retaining entered values | US-02, US-03 |
| Must | 08 Product Form — Save Failed | Create/update API request failed | Keep entered values, explain that the product was not saved, and let the user retry; do not show success or navigate away | US-10 |
| Must | 09 Delete — Confirm | User tapped Delete for a selected product | Confirmation names the actual selected product (dynamic placeholder {productName}), with Cancel and Delete actions; Cancel makes no change | US-04 |
| Must | 10 Delete — Failed | DELETE request failed | Keep the product visible and show an actionable error/retry path; never remove it optimistically | US-04, US-10 |
| Should | 11 Pantry — Search No Results | Optional name search has no matches | Show “No products found” and a “Clear search” action; preserve distinction from a pantry with zero records | US-05, US-12 |

US-06 — Filter by expiration status is Could priority and is not part of the required prototype baseline. Do not design filters as committed MVP functionality unless the scope is explicitly changed. If the existing Figma file already shows filters, label them as optional/deferred or remove them from the promised core flow.

## Transition and behavior rules

- App start → Pantry — Loading → either Pantry — Loaded, Pantry — Empty, or Pantry — Load Error.
- “Add product” opens the form in Add mode. “Edit” opens the same form in Edit mode, populated from the API-loaded item.
- Client validation errors appear without sending the request. API errors appear after request failure and preserve the form values.
- A successful create/update returns to the pantry list and shows a brief success message only after the API confirms success.
- “Delete” opens the confirmation alert for the selected product. Cancel closes the alert. Confirmed API success removes the row; API failure keeps it visible and displays Delete — Failed.
- Search and its no-results state are Should priority. Do not include a status-filter control until US-06 is promoted through scope review.
- The date status uses calendar dates in America/Bogota: expired before today; expiring soon today through the next three calendar days inclusive; not expiring soon after that; no expiration date when absent.

## Visual and accessibility checklist

- Keep English UI copy consistent across all states.
- Use an actual product name in delete confirmation, not a hardcoded “Whole Milk”.
- Ensure loading, empty, no-results, and error states look distinct.
- Give retry and primary actions clear labels; touch targets should meet the project's mobile accessibility guidance.
- Maintain readable contrast and do not rely on color alone to communicate expiration status.
- Keep the same form component/layout between add and edit variants; differentiate by title and populated values.
- Avoid mock interactions that imply persistence. The prototype illustrates intended UX; it is not an implemented API.

## Capture checklist for Activity 1

When the Figma file is updated, capture at least:
1. Loaded pantry/product list.
2. Add/edit form.
3. Delete confirmation.
4. Empty pantry.
5. Load/API error with retry.
6. Form validation and save failure.
7. Search no-results state, clearly marked Should priority.

Include the frame name and related story/priority in the screenshot caption or nearby annotation. The screenshots and prototype still need to be added to the Activity 1 submission material if the course deliverable expects evidence of those states.
