---
uc: UC-17
name: Net Worth
area: Finance
plan: PREMIUM
status: ✅ Complete
---

# UC-17 — Net Worth Items & History

**Actor:** PREMIUM authenticated user + System (monthly snapshot scheduler)
**Entry point:** `/net-worth`

---

## Main flow

### View

- FREE users see `<PremiumGate>`.
- PREMIUM: Assets section, Liabilities section, total net worth = assets − liabilities.
- History chart: line chart of monthly snapshots.

### Create item

1. Click "+ Add Asset" or "+ Add Liability".
2. Form: name, type (ASSET/LIABILITY), value, notes (optional).
3. `POST /api/net-worth/items` → totals recalculate.

### Edit / Delete

- `PUT /api/net-worth/items/{id}` · `DELETE /api/net-worth/items/{id}`.

### Monthly snapshot (scheduler)

- 1st of each month at 00:00.
- For each user with ≥1 net worth item: compute totals, insert `NetWorthSnapshot`.
- Snapshots power the history chart.

---

## Constraints

| Plan | Net worth items |
|---|---|
| FREE | ❌ |
| PREMIUM | ✅ Unlimited |

- Manual only — no bank/brokerage sync.
- Snapshots are monthly; no real-time or weekly history.
- No multi-currency conversion.
