# UC-14 — Tasks

- [x] `BudgetAlertService.checkBudgetAlerts()` — iterate users, calculate spend, check threshold
- [x] Debounce mechanism — track last alert date per budget
- [x] `EmailService.sendBudgetAlertEmail()` — email template with category/amount/%/limit
- [x] `FinanceScheduler` — `@Scheduled(cron = "0 0 9 * * *")` trigger
- [x] `budgetAlertsEnabled` + `budgetAlertThreshold` columns on `User` (part of UC-05)
- [x] Preferences UI toggle + threshold input (part of UC-05)
