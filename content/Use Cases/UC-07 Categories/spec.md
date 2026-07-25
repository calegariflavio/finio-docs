---
uc: UC-07
name: Categories
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-07 — Category Management

**Actor:** Authenticated user
**Entry point:** `/categories` · inline in transaction form

---

## Main flow

### View

1. `/categories` — categories grouped by type (INCOME / EXPENSE).
2. System defaults and user-created custom categories shown together.

### Create

1. Click "Add Category".
2. Enter name, type (INCOME/EXPENSE), icon/emoji.
3. `POST /api/categories` — scoped to current user.
4. Available immediately in transaction forms.

### Edit

1. Click edit on a custom category.
2. `PUT /api/categories/{id}`.

### Delete

1. Click delete on a custom category.
2. Confirmation → `DELETE /api/categories/{id}`.

### System categories

- Pre-seeded via Flyway migration, shared across all users.
- Cannot be edited or deleted.

---

## Constraints

- No category limit — users can create as many as they want.
- No category merging or bulk rename.
- Icon selection limited to a predefined emoji set.
