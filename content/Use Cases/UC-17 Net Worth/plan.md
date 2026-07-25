# UC-17 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/NetWorthPage.tsx` | Assets/liabilities lists, totals, history chart — wrapped in `PremiumGate` |
| `components/modals/NetWorthItemModal.tsx` | Create / edit form |
| `hooks/useNetWorth.ts` | Items query |
| `hooks/useNetWorthHistory.ts` | Snapshots query |

History chart: Recharts `LineChart` over `NetWorthSnapshot[]` ordered by month.

## Backend

| Layer | File |
|---|---|
| Scheduler | `FinanceScheduler` — `@Scheduled(cron = "0 0 0 1 * *")` |
| Controller | `NetWorthController` |
| Service | `NetWorthService` |
| Entity | `NetWorthItem` (id, userId, name, type, value, notes) |
| Entity | `NetWorthSnapshot` (id, userId, month, totalAssets, totalLiabilities, netWorth) |
| Migrations | `V15`, `V16` |

Plan guard on all write operations and the GET items endpoint.

## API

```
GET /api/net-worth/items       200 or 422
POST /api/net-worth/items      201 or 422
PUT /api/net-worth/items/{id}  200
DELETE /api/net-worth/items/{id} 200
GET /api/net-worth/history     200
```
