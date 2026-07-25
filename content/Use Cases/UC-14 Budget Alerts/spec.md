---
uc: UC-14
name: Budget Alerts
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-14 — Automated Budget Alert Emails

**Actor:** System (scheduled job) → user receives email
**Entry point:** Automatic — runs daily at 09:00

---

## Main flow

1. Daily at 09:00 — `FinanceScheduler` runs `BudgetAlertService.checkBudgetAlerts()`.
2. For each user with `budgetAlertsEnabled = true`:
   - Fetch all budgets for the current month.
   - Calculate spending per budget (same as [[UC-13 Budgets]]).
   - For each budget where `(spent / limit) × 100 ≥ budgetAlertThreshold`:
     - Check debounce (has an alert been sent today for this budget?).
     - If not: `EmailService.sendBudgetAlertEmail()` with category, amount, %, limit.
3. Debounce prevents repeat emails if job runs multiple times per day.

### User controls

Configured in Settings → Preferences ([[UC-05 Profile & Preferences]]):
- Toggle alerts on/off (`budgetAlertsEnabled`).
- Set threshold 1–100% (`budgetAlertThreshold`, default 80%).

---

## Constraints

- Email only — no in-app or push notification.
- Toggle is all-or-nothing (no per-budget disable).
- No alert history stored in the DB.
- SMTP currently broken locally — see [[Known Issues]].
