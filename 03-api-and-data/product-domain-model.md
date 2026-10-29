# Product Domain Model

- **Date:** 2026-10-25
- **Scope:** MVP product entity; no user accounts or pantry ownership.

## Product

| Field | Type | Required | Rules |
|---|---|---|---|
| `id` | String identifier | Server-generated | Stable identifier used by API routes |
| `name` | String | Yes | Trim whitespace; must not be blank |
| `quantity` | Number | Yes | Must be greater than zero; integer for Units and Packages; at most three decimal places for all quantities |
| `unit` | Enum | Yes | `unit`, `g`, `kg`, `ml`, `L`, or `package` |
| `category` | Enum | Yes | `produce`, `dairy`, `pantry_staples`, `canned_goods`, `bakery`, `meat_eggs`, or `snacks_other` |
| `expirationDate` | Calendar date (`YYYY-MM-DD`) or null | No | Optional; valid calendar date; past dates are allowed |
| `createdAt` | ISO 8601 timestamp | Server-managed | Set when created; clients do not submit it |
| `updatedAt` | ISO 8601 timestamp | Server-managed | Set on create/update; clients do not submit it |

Wire values are fixed, lowercase machine-readable keys as shown above (except unit symbol `L`, which is uppercase). The UI may display friendly labels and must map them to these wire values. Do not silently accept arbitrary units/categories or perform unit conversion.

### Display labels

| Wire value | Display label |
|---|---|
| `unit` | Units |
| `g` | Grams (g) |
| `kg` | Kilograms (kg) |
| `ml` | Milliliters (ml) |
| `L` | Liters (L) |
| `package` | Packages |
| `produce` | Produce |
| `dairy` | Dairy |
| `pantry_staples` | Pantry Staples |
| `canned_goods` | Canned Goods |
| `bakery` | Bakery |
| `meat_eggs` | Meat & Eggs |
| `snacks_other` | Snacks & Other |

## Expiration status (derived, not persisted)

Use the current calendar date in the `America/Bogota` time zone. Compare calendar dates, not UTC instants:

- **Expired:** `expirationDate < today`.
- **Expiring Soon:** today through three calendar days from today, inclusive.
- **Not Expiring Soon:** after today + 3 calendar days.
- **No Expiration Date:** `expirationDate` is absent or null.

Calculate status in a domain function so boundary dates can be unit-tested with a fixed clock. Status is derived; clients must not submit it to the API or persist it as a field.

## Validation rules

- Trim the product name before saving; reject a blank result.
- `quantity > 0` and supports at most three decimal places, matching PostgreSQL `NUMERIC(12, 3)`.
- `unit` and `category` must belong to their fixed allowed lists.
- For `unit` and `package`, quantity must be an integer; `g`, `kg`, `ml`, and `L` may use decimals.
- If present, expiration date must be a real calendar date in `YYYY-MM-DD` format. Past dates remain valid for already-expired stock.
- API validation is mandatory even when the mobile client validates the same rules.

## MVP data boundary

There is one demonstration pantry and no users, accounts, pantry-sharing, or per-user isolation. Product names are not unique. This is demonstration-only data, not a design for public multi-user production use.
