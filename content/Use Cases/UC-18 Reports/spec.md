---
uc: UC-18
name: Reports
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-18 — Monthly Financial Reports

**Actor:** Authenticated user + System (1st of month scheduler)
**Entry point:** `/reports`

---

## Main flow

### View reports

1. `GET /api/reports` — list of generated reports, newest first.
2. Each card: month/year, income total, expense total, net balance.

### Report detail

1. Click a report → `GET /api/reports/{id}`.
2. Income vs. expense bar chart, category breakdown, comparison to previous month (% change).

### Generation (scheduler)

- 1st of each month at 01:00.
- For each user with transactions in the previous month:
  - Aggregate: income, expenses, net, category breakdown.
  - Create `MonthlyReport` record.
  - Send report email.

---

## Constraints

- Generated automatically — no on-demand regeneration from the UI.
- No PDF export (deferred — see [[Future Roadmap]]).
- Email delivery requires working SMTP — see [[Known Issues]].
- `categoryBreakdown` stored as JSON column (not queryable at DB level without JSON operators).
