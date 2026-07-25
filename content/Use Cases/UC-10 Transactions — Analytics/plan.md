# UC-10 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/TransactionsPage.tsx` | Hosts Summary + Calendar tabs |
| `pages/AnalyticsPage.tsx` | Category breakdown + trend charts |
| `hooks/useSummary.ts` | Monthly totals |
| `hooks/useCalendar.ts` | Day-by-day map |
| `hooks/useAnalytics.ts` | Category breakdown + monthly trend |

Charts use Recharts: `PieChart`/`ResponsiveContainer` for category donut, `BarChart` for income/expense trend.

## Backend

All three are read-only aggregation endpoints on `TransactionController`. No dedicated entity — data computed on the fly from the transactions table using JPQL aggregation queries.

```
GET /api/transactions/summary?month=2026-07
→ { totalIncome, totalExpenses, netBalance, transactionCount }

GET /api/transactions/calendar?month=2026-07
→ { "2026-07-01": { income, expense, net }, ... }

GET /api/transactions/analytics?startDate=...&endDate=...
→ { byCategory: [{ categoryName, total }], monthlyTrend: [{ month, income, expense }] }
```
