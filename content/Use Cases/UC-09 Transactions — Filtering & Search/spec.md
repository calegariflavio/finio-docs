---
uc: UC-09
name: Transactions — Filtering & Search
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-09 — Transaction Filtering & Search

**Actor:** Authenticated user
**Entry point:** `/transactions` toolbar

---

## Main flow

1. Toolbar exposes filter controls:
   - **Search** — free text against description + notes.
   - **Type** — All / Income / Expense.
   - **Category** — dropdown.
   - **Date range** — from / to pickers.
   - **Amount range** — min / max inputs.
   - **Tags** — tag input.
2. Filters are query params on `GET /api/transactions` — table updates reactively.
3. Active filters shown as dismissable chips.
4. "Clear all filters" resets to defaults.

### Sorting

Clicking a column header (Date, Amount, Description, Category) sorts ASC/DESC. Passed as `sort=field,direction`.

---

## Constraints

- Filter state is local — not URL-persisted (refreshing resets filters).
- No saved filter presets.
- Full-text search covers description + notes only (not category name, tags).
- Tag search is exact match per tag, not fuzzy.
- All filters applied with AND logic.
