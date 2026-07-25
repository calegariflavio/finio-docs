# UC-15 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/GoalsPage.tsx` | Goals list, wrapped in `<PremiumGate>` |
| `components/modals/GoalModal.tsx` | Create / edit form |
| `hooks/useGoals.ts` | React Query hook |
| `hooks/useGoalMutations.ts` | CRUD mutations |

`PremiumGate` blurs children and overlays an upgrade CTA for FREE users. PREMIUM users see the full page.

## Backend

| Layer | File |
|---|---|
| Controller | `GoalController` |
| Service | `GoalService` |
| Entity | `Goal` (id, userId, name, targetAmount, currentAmount, deadline, notes) |
| Migration | `V12__create_goals_table.sql` |

Plan guard: `GoalService.createGoal()` calls `planGuard.requirePremium(user)` → throws `PlanUpgradeRequiredException` (422).

## API

```
GET /api/goals            200 or 422
POST /api/goals           201 or 422
PUT /api/goals/{id}       200
DELETE /api/goals/{id}    200
```
