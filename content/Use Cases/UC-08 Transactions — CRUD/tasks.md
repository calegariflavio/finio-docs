# UC-08 — Tasks

- [x] `V6__create_transactions_table.sql`
- [x] `V7__create_transaction_tags_table.sql`
- [x] `Transaction` entity, `TransactionRepository`
- [x] `TransactionController` + `TransactionService` — CRUD
- [x] Duplicate detection logic in `createTransaction()`
- [x] `DELETE /api/transactions/bulk` endpoint
- [x] `TransactionsPage.tsx` — table, sorting, pagination, bulk select
- [x] `TransactionModal.tsx` — create/edit form (type, category, amount, date, description, tags, notes)
- [x] `useTransactions` hook — paginated, accepts filter params
- [x] `useTransactionMutations` hook — invalidates query on success
- [x] Toast text handles both "created" and "possible duplicate" responses
