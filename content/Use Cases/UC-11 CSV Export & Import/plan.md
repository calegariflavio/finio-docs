# UC-11 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/TransactionsPage.tsx` | Export button; import button inside `<PremiumGate>` |
| `components/modals/ImportModal.tsx` | File picker + result display |
| `components/PremiumGate.tsx` | Blurs import UI for FREE users, shows upgrade CTA |

## Backend

| Layer | File |
|---|---|
| Controller | `TransactionController.exportCsv()`, `TransactionController.importCsv()` |
| Service | `CsvExportService`, `CsvImportService` |

**Export:** Applies the same `TransactionSpecification` as the list endpoint. Builds a CSV string in memory (acceptable for typical dataset sizes) and writes to the response as `text/csv`.

**Import:** `multipart/form-data` input. `CsvImportService` streams the CSV, processes each row independently. Unknown category names are created on the fly. Row-level errors are collected and returned in the response rather than aborting the whole import.

Plan guard: `CsvImportService.importCsv()` calls `planGuard.requirePremium(user)` — throws `PlanUpgradeRequiredException` (422) for FREE users.

## API

```
GET /api/transactions/export/csv?...filters  →  text/csv download
POST /api/transactions/import/csv  (multipart)  →  { imported, failed, errors }
422: user is FREE
```
