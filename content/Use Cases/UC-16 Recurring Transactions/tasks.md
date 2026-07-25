# UC-16 — Tasks

- [x] `V13__create_recurring_transactions_table.sql`
- [x] `RecurringTransaction` entity + repository
- [x] `RecurringTransactionController` + `RecurringTransactionService` — CRUD + plan guard
- [x] `processRecurring()` — find due templates, create transactions, update next due date
- [x] `FinanceScheduler` — `@Scheduled(cron = "0 30 0 * * *")` trigger
- [x] `RecurringPage.tsx` — template list, wrapped in `PremiumGate`
- [x] `RecurringModal.tsx` — create/edit form with frequency enum select
- [x] `useRecurring` + `useRecurringMutations` hooks
