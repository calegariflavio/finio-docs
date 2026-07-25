# UC-08 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/TransactionsPage.tsx` | Table, toolbar, pagination, bulk select |
| `components/modals/TransactionModal.tsx` | Create / edit form |
| `hooks/useTransactions.ts` | Paginated React Query hook |
| `hooks/useTransactionMutations.ts` | Create, update, delete, bulk-delete |

## Backend

| Layer | File |
|---|---|
| Controller | `TransactionController` |
| Service | `TransactionService` |
| Entity | `Transaction` (id, userId, categoryId, type, amount, date, description, notes) |
| Entity | `transaction_tags` join table (transaction_id, tag) |
| Repository | `TransactionRepository` — JPA + Specifications for filtering |
| Migrations | `V6__create_transactions_table.sql`, `V7__create_transaction_tags_table.sql` |

Duplicate detection: `TransactionService.createTransaction()` checks for an existing transaction with the same `userId + categoryId + amount` within a configurable recent window (e.g. 7 days). Returns the created transaction with a `possibleDuplicate: true` flag; frontend chooses the toast text.

## API

```
GET /api/transactions?page=0&size=20&sort=date,desc
201 POST, 200 PUT, 200 DELETE /api/transactions/{id}
DELETE /api/transactions/bulk  Body: { ids: string[] }
```
