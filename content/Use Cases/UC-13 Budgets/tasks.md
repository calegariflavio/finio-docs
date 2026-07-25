# UC-13 — Tasks

- [x] `V9__create_budgets_table.sql`
- [x] `Budget` entity + `BudgetRepository`
- [x] `BudgetController` + `BudgetService` — CRUD
- [x] `getBudgetsWithSpending()` — aggregate spend from transactions per budget
- [x] FREE plan limit: count check in `createBudget()`, throw `PlanUpgradeRequiredException` at ≥3
- [x] `POST /api/budgets/copy-previous` — clone last month's budgets
- [x] `BudgetsPage.tsx` — budget cards with progress bars, colour coding
- [x] `BudgetModal.tsx` — create/edit form
- [x] `useBudgets` + `useBudgetMutations` hooks
- [x] Month picker (defaults to current month)
