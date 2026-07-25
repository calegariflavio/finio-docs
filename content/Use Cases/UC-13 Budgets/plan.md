# UC-13 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/BudgetsPage.tsx` | Budget cards, toolbar, month picker |
| `components/modals/BudgetModal.tsx` | Create / edit form |
| `hooks/useBudgets.ts` | React Query hook |
| `hooks/useBudgetMutations.ts` | CRUD + copy mutations |

## Backend

| Layer | File |
|---|---|
| Controller | `BudgetController` |
| Service | `BudgetService` |
| Entity | `Budget` (id, userId, categoryId, monthlyLimit, month) |
| Migration | `V9__create_budgets_table.sql` |

`BudgetService.getBudgetsWithSpending()` joins the transactions table to compute `spent` and `remaining` per budget. The FREE limit check is in `createBudget()`: counts existing budgets for `(userId, currentMonth)` — if ≥ 3, throws `PlanUpgradeRequiredException`.

## API

```
GET /api/budgets?month=2026-07
201 POST, 200 PUT, 200 DELETE /api/budgets/{id}
POST /api/budgets/copy-previous
422: FREE plan limit exceeded
```
