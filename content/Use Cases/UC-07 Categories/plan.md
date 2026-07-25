# UC-07 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/CategoriesPage.tsx` | Grouped list by type |
| `components/modals/CategoryModal.tsx` | Create / edit form |
| `hooks/useCategories.ts` | React Query — also used by transaction form |

## Backend

| Layer | File |
|---|---|
| Controller | `CategoryController` |
| Service | `CategoryService` |
| Entity | `Category` (id, userId nullable, name, type enum, icon, isDefault) |
| Migrations | `V4__create_categories_table.sql`, `V5__seed_default_categories.sql` |

Query pattern: `WHERE user_id = :userId OR user_id IS NULL` — returns both user-owned and default categories. Default categories have `user_id = null` and `is_default = true`.

## API

```
GET /api/categories
200: { data: Category[] }

POST /api/categories
Body: { name, type, icon }
201: { data: Category }

PUT /api/categories/{id}
Body: { name, type, icon }
200: { data: Category }

DELETE /api/categories/{id}
200: success
403: attempt to delete default category
```
