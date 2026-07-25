---
uc: UC-10
name: Transactions — Analytics
area: Finance
plan: FREE
status: ✅ Complete
---

# UC-10 — Summary, Calendar, Trends

**Actor:** Authenticated user
**Entry point:** `/transactions` tabs · `/analytics`

---

## Main flow

### Monthly summary

- Summary tab on `/transactions`.
- `GET /api/transactions/summary?month=2026-07`.
- Stat cards: total income, total expenses, net balance for the month.

### Calendar view

- Calendar tab on `/transactions`.
- `GET /api/transactions/calendar?month=2026-07`.
- Monthly grid — each day cell shows net amount (green/red).
- Click a day → popover lists that day's transactions.

### Analytics (trends)

- `/analytics` page.
- `GET /api/transactions/analytics?startDate=...&endDate=...`.
- Spending by category (donut chart).
- Monthly income vs. expense (bar chart, last 6–12 months).
- Rendered with Recharts.

---

## Constraints

- Calendar is month-navigation only — no arbitrary date range.
- No year-over-year comparison.
- No export of chart data as image/PDF.
