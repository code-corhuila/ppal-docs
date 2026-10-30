# Design System

## Purpose and source of truth

PantryPal uses Ionic's CSS custom properties with a project-level token layer. The source of truth for visual tokens is `src/theme/variables.scss`; screen styles must consume tokens rather than introduce one-off colors, spaces, radii, or typography values.

The existing Figma prototype uses a dark-green primary action, light surfaces, product cards, status labels, and modal/alert patterns. Exact color hex values and measured contrast ratios are intentionally not copied from screenshots; they must be read from the selected Figma styles and measured during the final visual review before they are recorded as verified.

## Token groups

| Token group | PantryPal token examples | Rule |
|---|---|---|
| Color | Ionic `--ion-color-primary`, `--ion-color-primary-contrast`, `--ion-color-success`, `--ion-color-warning`, `--ion-color-danger`, surface/text tokens | Define in one theme file; pair status color with a visible text label |
| Spacing | `--ppal-space-xs`, `--ppal-space-sm`, `--ppal-space-md`, `--ppal-space-lg`, `--ppal-space-xl` | Use a fixed spacing scale instead of per-screen numbers |
| Radius | `--ppal-radius-sm`, `--ppal-radius-card`, `--ppal-radius-modal`, `--ppal-radius-pill` | Shared card, form, dialog, and status-label geometry |
| Typography | Ionic typography plus `--ppal-text-title`, `--ppal-text-body`, `--ppal-text-caption` | Body text scales with OS settings; avoid fixed-height text containers |
| Elevation | `--ppal-elevation-card`, `--ppal-elevation-dialog` | Use a small consistent elevation scale |

These are token names for the design specification. Keep actual values in `src/theme/variables.scss`; do not duplicate raw values in feature styles.

## Ionic modes: md and ios

- Preserve a shared component tree and shared tokens; do not create separate pages for Android and iOS.
- Use Ionic's platform modes (`md` and `ios`) for platform-appropriate controls and native-feeling details.
- Where a genuine visual difference is needed, define it through the Ionic mode selector and document the reason. Business behavior and navigation must remain the same.
- Review the pantry list, product form, delete confirmation, and error/empty states in both modes before the release checklist is closed.
- Respect safe areas, system gestures, keyboard behavior, and the platform back interaction.

## Light and dark appearance

Use the same token names in light and dark appearance; change their values rather than duplicating components. The final prototype review must verify the primary, surface, error, and expiration-state colors in both appearances.

## Accessibility requirements

- Normal text contrast target: at least 4.5:1; large text and relevant graphical elements: at least 3:1 (WCAG AA).
- Touch targets: at least 44 × 44 pt on iOS and 48 × 48 dp on Android.
- Never communicate Expired / Expiring Soon / Not Expiring Soon / No Expiration Date through color alone; retain a readable text label.
- Inputs need programmatic labels, field-level validation messages, and clear focus order.
- Dialogs must be accessible to screen readers and return focus appropriately.
- Text must scale, and loading/error/empty/no-results states must remain distinct and readable.

## Verification status

- [ ] Copy the final color values from the approved Figma styles into `src/theme/variables.scss`.
- [ ] Measure primary/background contrast in light and dark appearances and record the ratios.
- [ ] Review md and ios modes at the primary list/form/dialog flows.
- [ ] Verify text scaling, keyboard and screen-reader interaction.
- [ ] Capture final Figma screenshots for the H2 evidence.

No contrast result or device-level accessibility pass is claimed until the corresponding check is performed.
