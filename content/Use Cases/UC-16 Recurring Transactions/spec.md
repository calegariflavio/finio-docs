---
uc: UC-16
name: Recurring Transactions
area: Finance
plan: PREMIUM
status: ✅ Complete
---

# UC-16 — Recurring Transaction Templates

**Actor:** PREMIUM authenticated user + System (scheduler)
**Entry point:** `/recurring`

---

## Main flow

### View

- FREE users see `<PremiumGate>`.
- PREMIUM: list of templates — name, amount, category, frequency, next due date, active status.

### Create

1. Click "+ Add".
2. Form: name, type (INCOME/EXPENSE), category, amount, frequency (DAILY/WEEKLY/MONTHLY/YEARLY), start date, end date (optional).
3. `POST /api/recurring`.

### Edit / Pause / Delete

- Edit: `PUT /api/recurring/{id}`.
- Pause: `PUT /api/recurring/{id}` with `{ active: false }`.
- Delete: `DELETE /api/recurring/{id}` — removes template only, not generated transactions.

### Automatic generation (scheduler)

- Daily at 00:30 — `RecurringTransactionService.processRecurring()`.
- For each active template where today ≥ `nextDueDate`:
  - Create a real transaction in the transactions table.
  - Update `lastProcessedDate` and calculate new `nextDueDate`.

---

## Constraints

| Plan | Recurring templates |
|---|---|
| FREE | ❌ (1-2 for FREE is under consideration — see [[Future Roadmap]]) |
| PREMIUM | ✅ Unlimited |

- Generated transactions are not linked back to their template (no FK).
- No "skip next occurrence".
- No catch-up for missed due dates (server downtime).
