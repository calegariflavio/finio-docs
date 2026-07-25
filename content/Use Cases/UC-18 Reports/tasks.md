# UC-18 — Tasks

- [x] `V18__create_monthly_reports_table.sql`
- [x] `MonthlyReport` entity + repository
- [x] `ReportController` + `MonthlyReportService`
- [x] `generateMonthlyReports()` — aggregate per user, persist report
- [x] `EmailService.sendMonthlyReportEmail()` — email template with income/expense/net summary
- [x] `FinanceScheduler` — `@Scheduled(cron = "0 0 1 1 * *")` trigger
- [x] `POST /api/reports/generate` manual trigger endpoint
- [x] `ReportsPage.tsx` — list with month/income/expense/net
- [x] `ReportDetailPage.tsx` — bar chart + category breakdown + MoM comparison
- [x] `useReports` hook
