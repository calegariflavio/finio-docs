# UC-09 — Implementation Plan

## Frontend

Filter state is `useState` in `TransactionsPage.tsx` and passed as params to `useTransactions(filters)`. React Query refetches whenever the filter object changes.

Active filter chips are derived from the filter state — each chip has an `onDismiss` handler that removes that filter key.

## Backend

`TransactionController.getTransactions()` accepts all filter params as `@RequestParam`. `TransactionService` passes them to a JPA Specification builder that dynamically constructs the WHERE clause — only non-null params generate a predicate.

Spring Data `Pageable` handles pagination + sorting.

```
GET /api/transactions
  ?search=coffee
  &type=EXPENSE
  &categoryId=uuid
  &startDate=2026-01-01&endDate=2026-12-31
  &minAmount=10&maxAmount=500
  &tags=food,travel
  &page=0&size=20&sort=date,desc
```
