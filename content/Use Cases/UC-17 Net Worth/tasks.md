# UC-17 — Tasks

- [x] `V15__create_net_worth_items_table.sql`
- [x] `V16__create_net_worth_snapshots_table.sql`
- [x] `NetWorthItem` + `NetWorthSnapshot` entities + repositories
- [x] `NetWorthController` + `NetWorthService` — CRUD + plan guard
- [x] `createMonthlySnapshot()` — aggregate items per user, insert snapshot
- [x] `FinanceScheduler` — `@Scheduled(cron = "0 0 0 1 * *")` trigger
- [x] `NetWorthPage.tsx` — assets/liabilities sections, totals, history chart, wrapped in `PremiumGate`
- [x] `NetWorthItemModal.tsx` — create/edit form
- [x] `useNetWorth` + `useNetWorthHistory` hooks
