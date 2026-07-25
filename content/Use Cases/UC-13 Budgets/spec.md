---
uc: UC-13
name: Budgets
area: Finance
plan: FREE (max 3) / PREMIUM (unlimited)
status: ✅ Complete
---

# UC-13 — Budget CRUD & Spending Tracking

**Actor:** Authenticated user
**Entry point:** `/budgets`

---

## Main flow

### View

- Budget cards: category, monthly limit, progress bar (spent / limit), colour coding.
- Amber at ≥80%, red at ≥100%.

### Create

1. Click "+ Add Budget" → modal: category, monthly limit.
2. `POST /api/budgets`.
3. FREE users with 3 active budgets → `422 PlanUpgradeRequiredException`.

### Edit / Delete

- `PUT /api/budgets/{id}` · `DELETE /api/budgets/{id}`.

### Copy from previous month

- "Copy from last month" → `POST /api/budgets/copy-previous`.
- Bulk-creates this month's budgets from last month's definitions.

### Spending tracking

- Spending is derived live: sum of EXPENSE transactions this month matching the budget's category.
- No separate budget-transaction entity.

---

## Constraints

| Plan | Budgets per month |
|---|---|
| FREE | 3 |
| PREMIUM | Unlimited |

- No budget rollover (deferred — see [[Future Roadmap]]).
- Copy-from-previous does not check for duplicate budgets in the target month.
