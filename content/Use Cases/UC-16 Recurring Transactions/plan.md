# UC-16 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/RecurringPage.tsx` | Templates list, wrapped in `PremiumGate` |
| `components/modals/RecurringModal.tsx` | Create / edit form |
| `hooks/useRecurring.ts` | React Query hook |
| `hooks/useRecurringMutations.ts` | CRUD mutations |

## Backend

| Layer | File |
|---|---|
| Scheduler | `FinanceScheduler` — `@Scheduled(cron = "0 30 0 * * *")` |
| Controller | `RecurringTransactionController` |
| Service | `RecurringTransactionService` |
| Entity | `RecurringTransaction` (id, userId, categoryId, type, name, amount, frequency, startDate, endDate, nextDueDate, lastProcessedDate, active) |
| Migration | `V13__create_recurring_transactions_table.sql` |

`processRecurring()`: fetches all active templates where `nextDueDate ≤ today`, creates a `Transaction`, then computes the next due date based on `frequency` using `java.time` date arithmetic.

## API

```
GET /api/recurring            200 or 422
POST /api/recurring           201 or 422
PUT /api/recurring/{id}       200
DELETE /api/recurring/{id}    200
```
