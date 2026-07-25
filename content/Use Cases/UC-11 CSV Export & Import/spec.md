---
uc: UC-11
name: CSV Export & Import
area: Finance
plan: FREE (export) / PREMIUM (import)
status: ✅ Complete
---

# UC-11 — CSV Export (FREE) and Import (PREMIUM)

**Actor:** Authenticated user
**Entry point:** Transactions page toolbar

---

## Main flow

### Export (FREE)

1. Click "Export CSV".
2. Active filters apply — exports filtered set.
3. `GET /api/transactions/export/csv` (same filter params as current view).
4. Backend builds CSV: `Date,Type,Category,Amount,Description,Notes,Tags`.
5. Browser downloads `transactions.csv`.

### Import (PREMIUM)

1. `<PremiumGate feature="csvImport">` wraps the button — FREE users see blurred upgrade CTA.
2. PREMIUM user opens import modal, selects a CSV file.
3. `POST /api/transactions/import/csv` (multipart/form-data).
4. Backend parses rows, validates required fields, maps category names (creates unknown categories), inserts valid rows.
5. Response: `{ imported: N, failed: M, errors: [{ row, message }] }`.
6. Toast summary + table refresh.

---

## Constraints

| Feature | FREE | PREMIUM |
|---|---|---|
| Export CSV | ✅ | ✅ |
| Import CSV | ❌ | ✅ |

- No CSV template download.
- Import does not deduplicate (re-importing creates duplicate rows).
- No other formats (OFX, QIF, XLSX).
