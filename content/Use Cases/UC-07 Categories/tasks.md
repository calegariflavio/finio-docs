# UC-07 — Tasks

- [x] `V4__create_categories_table.sql` — id, user_id (nullable), name, type, icon, is_default
- [x] `V5__seed_default_categories.sql` — seed INCOME + EXPENSE default categories
- [x] `Category` entity + `CategoryRepository`
- [x] `CategoryController` + `CategoryService` (CRUD)
- [x] Guard: cannot edit/delete categories where `isDefault = true`
- [x] `CategoriesPage.tsx` — grouped by type, edit/delete actions
- [x] `CategoryModal.tsx` — create/edit form with emoji picker
- [x] `useCategories` hook — shared with transaction form dropdown
