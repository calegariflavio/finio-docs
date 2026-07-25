# UC-18 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/ReportsPage.tsx` | Report list |
| `pages/ReportDetailPage.tsx` | Charts + breakdown for a single report |
| `hooks/useReports.ts` | React Query hook |

## Backend

| Layer | File |
|---|---|
| Scheduler | `FinanceScheduler` — `@Scheduled(cron = "0 0 1 1 * *")` |
| Controller | `ReportController` |
| Service | `MonthlyReportService` |
| Entity | `MonthlyReport` (id, userId, month, totalIncome, totalExpenses, netBalance, categoryBreakdown JSON) |
| Migration | `V18__create_monthly_reports_table.sql` |

`categoryBreakdown` is a JSON column containing `[{ categoryName, amount }]`. Serialized/deserialized by Jackson via a JPA `@Convert` or `@Column(columnDefinition = "jsonb")`.

`generateMonthlyReports()` is also exposed as an admin endpoint (`POST /api/reports/generate`) for manual triggering.

## API

```
GET /api/reports          200: { data: MonthlyReport[] }
GET /api/reports/{id}     200: { data: MonthlyReport }
POST /api/reports/generate  Body: { month }  (admin/manual)
```
