# UC-11 — Tasks

- [x] `CsvExportService` — build CSV string from filtered transactions
- [x] `TransactionController.exportCsv()` — `text/csv` response with `Content-Disposition`
- [x] `CsvImportService` — stream-parse CSV, validate rows, create unknown categories, collect errors
- [x] `TransactionController.importCsv()` — `multipart/form-data`, plan guard
- [x] Export button in `TransactionsPage.tsx` toolbar
- [x] `ImportModal.tsx` — file picker + result summary (imported/failed counts + error list)
- [x] `PremiumGate` wrapping the import button
