---
uc: UC-08
name: Transactions — CRUD
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-08 — Transaction Create, Read, Update, Delete

**Actor:** Authenticated user
**Entry point:** `/transactions`

---

## Main flow

### List

- Paginated table: date, description, category, amount (red/green by type), tags.
- Columns sortable. Pagination controls at bottom.

### Create

1. Click "+ Add" (toolbar, `{ exact: true }` — distinct from empty-state button).
2. Modal form: type, category, amount, date, description (optional), tags (optional), notes (optional).
3. `POST /api/transactions`.
4. Duplicate detection: same category + amount within a recent window → "Transaction saved — looks like a possible duplicate." toast. Otherwise "Transaction created".
5. Table refreshes (React Query invalidation).

### Edit

1. Click row → edit modal pre-filled.
2. `PUT /api/transactions/{id}` → toast + refresh.

### Delete

1. Click delete icon → confirmation → `DELETE /api/transactions/{id}`.

### Bulk delete

1. Check multiple rows → "Delete selected" toolbar button.
2. Confirmation with count.
3. `DELETE /api/transactions/bulk` with `{ ids: [...] }`.

---

## Constraints

- No transaction count limit (FREE).
- No undo after delete.
- No split transactions (deferred — see [[Future Roadmap]]).
- Tags are free-text, no predefined list.
