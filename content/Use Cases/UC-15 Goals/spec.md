---
uc: UC-15
name: Goals
area: Finance
plan: PREMIUM
status: ✅ Complete
---

# UC-15 — Savings Goals

**Actor:** PREMIUM authenticated user
**Entry point:** `/goals`

---

## Main flow

### View

- FREE users see `<PremiumGate>` — blurred content + upgrade CTA.
- PREMIUM: goal cards showing name, target, current saved, deadline, progress bar.
- Cards marked "Completed" when `currentAmount ≥ targetAmount`.

### Create

1. Click "+ Add Goal".
2. Form: name, target amount, initial current amount, deadline, notes (optional).
3. `POST /api/goals`.

### Edit / Delete

- `PUT /api/goals/{id}` · `DELETE /api/goals/{id}`.

---

## Constraints

| Plan | Goals |
|---|---|
| FREE | ❌ |
| PREMIUM | ✅ Unlimited |

- Goals are manual — no automatic contribution from transactions.
- Only current amount stored (no contribution history).
- No milestone notifications.
