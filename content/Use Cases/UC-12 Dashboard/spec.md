---
uc: UC-12
name: Dashboard
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-12 — Dashboard Stat Cards

**Actor:** Authenticated user
**Entry point:** `/dashboard` (default after login)

---

## Main flow

1. Navigate to `/dashboard`.
2. Four stat cards for the current month: Income, Expenses, Balance, Transaction count.
3. Recent transactions table (last 5, quick link to full list).
4. Spending by category donut chart (current month).

---

## Data sources

| Widget | Endpoint |
|---|---|
| Stat cards | `GET /api/transactions/summary?month=<current>` |
| Recent transactions | `GET /api/transactions?page=0&size=5&sort=date,desc` |
| Donut chart | `GET /api/transactions/analytics?startDate=...&endDate=...` |

React Query `staleTime: 0` — data always refetches on page mount.

---

## Constraints

- Month is always the current calendar month — no picker.
- No customisable widget layout.
- No month-over-month comparison cards.
- No upcoming recurring transactions widget.
