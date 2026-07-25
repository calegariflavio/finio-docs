# UC-12 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/DashboardPage.tsx` | Stat cards, recent transactions table, donut chart |
| `hooks/useSummary.ts` | Monthly totals (shared with UC-10) |
| `hooks/useTransactions.ts` | Recent 5 (shared with UC-08) |
| `hooks/useAnalytics.ts` | Category breakdown (shared with UC-10) |

All three hooks use `staleTime: 0` so data is always fresh on mount.

## Backend

No new endpoints — the dashboard is entirely composed from existing endpoints from [[UC-08 Transactions — CRUD]] and [[UC-10 Transactions — Analytics]]. No dedicated dashboard controller.
