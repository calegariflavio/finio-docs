# UC-14 — Implementation Plan

## Backend

| Layer | File |
|---|---|
| Scheduler | `FinanceScheduler` — `@Scheduled(cron = "0 0 9 * * *")` |
| Service | `BudgetAlertService.checkBudgetAlerts()` |
| Email | `EmailService.sendBudgetAlertEmail()` |

The debounce mechanism: a column or flag tracks the last alert date per budget. If `lastAlertDate = today`, skip. This prevents duplicate emails during server restarts or accidental double-schedules.

## Frontend

No dedicated page — alert settings live in the Preferences tab (`PreferencesTab.tsx`) as part of [[UC-05 Profile & Preferences]].

## Key decisions

- Per-user threshold (not per-budget) keeps the data model simple.
- Debounce is per-day, per-budget (not per-user) — multiple budgets can alert on the same day.
