# UC-09 — Tasks

- [x] `TransactionSpecification` — JPA Specifications for each filter param (search, type, category, date range, amount range, tags)
- [x] `TransactionController.getTransactions()` — accept all filter `@RequestParam`s, pass `Pageable`
- [x] Filter toolbar in `TransactionsPage.tsx` — search, type, category, date range, amount range, tags
- [x] Active filter chips with per-chip dismiss
- [x] "Clear all filters" button
- [x] `useTransactions(filters)` hook — passes params to API call, refetches on filter change
- [x] Column sort headers (Date, Amount, Description, Category) — ASC/DESC toggle
