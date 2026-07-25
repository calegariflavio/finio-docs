# Finio — Testing Strategy

*Last updated: 2026-07-25*

---

## Overview

| Layer | Tool | Run command |
|---|---|---|
| Frontend unit tests | Vitest + Testing Library | `npm run test` (from `frontend/`) |
| Frontend E2E tests | Playwright | `npm run test:e2e` (from `frontend/`) |
| Backend tests | Maven + JUnit | `mvn test` (from `backend/`) |

---

## Playwright E2E Tests

### Setup

The E2E suite requires:
1. Backend running on `:8080`
2. Frontend dev server on `:5173` (`npm run dev`)
3. Test user in the database (see below)

Config: `frontend/playwright.config.ts`
- Headless Chromium, single worker, no retries
- Screenshots on failure only
- Base URL: `http://localhost:5173`

### Test user

```
Email:    playwright@finio.dev
Password: Password123!
Plan:     PREMIUM
```

Inserted directly into the DB (SMTP was broken during setup):

```sql
INSERT INTO users (id, email, password_hash, full_name, plan, role,
                   email_verified, budget_alerts_enabled, budget_alert_threshold,
                   currency, theme, created_at, updated_at)
VALUES (
  gen_random_uuid(),
  'playwright@finio.dev',
  '$2b$10$U/rZuGao7N/lqVNdhEwvaehP26VnFm/.N8aCDngvWibrsJWm91bD6',
  'Playwright Tester',
  'PREMIUM',
  'USER',
  true,
  true,
  80,
  'CAD',
  'system',
  now(),
  now()
);
```

### Test files

| File | What it covers |
|---|---|
| `e2e/helpers.ts` | `login()` helper used by all authenticated specs |
| `e2e/auth.spec.ts` | Login success, wrong password error, invalid email format, unauthenticated redirect, logout, forgot-password link |
| `e2e/dashboard.spec.ts` | Stat cards, sidebar nav links, navigation to transactions and settings |
| `e2e/transactions.spec.ts` | Table headers, open modal, validate required fields, add expense, search empty state, type filter |
| `e2e/password-validation.spec.ts` | Register checklist appears on typing, all rules turn green, weak password blocks submit, forgot-password step 2 shows checklist |

### Current pass rate

**20 / 20 passing** (as of 2026-07-25)

### Known test quirks

- **Forgot-password test** mocks `/api/auth/forgot-password` via `page.route()` because SMTP is broken locally and the endpoint returns 500. Remove the mock once SMTP is fixed.
- **Transaction add test** accepts both `"Transaction created"` and `"Transaction saved — looks like a possible duplicate."` toasts because the test user accumulates transactions across runs (same category/amount/date triggers duplicate detection).
- **Add button selector** uses `{ exact: true }` to distinguish the toolbar "+ Add" button from the empty-state "Add transaction" button.

---

## Vitest Unit Tests

Located in `frontend/src/` alongside source files, or in `__tests__/` folders.

Key test files:
- `src/context/AuthContext.test.tsx` — auth context state management
- `src/components/PremiumGate.test.tsx` — plan-based rendering logic

Run with coverage:

```bash
npm run test:coverage
```

---

## Backend Tests

Located in `backend/src/test/`. Currently minimal — the service layer is largely covered by manual + E2E testing rather than unit tests.

```bash
mvn test
mvn test -Dtest=ClassName   # run a specific class
```

---

## What's NOT covered

| Area | Notes |
|---|---|
| Budgets page E2E | Not written yet |
| Goals page E2E | Not written yet |
| Recurring transactions E2E | Not written yet |
| Net worth page E2E | Not written yet |
| CSV import E2E | Not written yet |
| Admin panel E2E | Not written yet |
| Stripe checkout flow E2E | Requires Stripe test mode + CLI webhook forwarding |
| Backend service unit tests | Mostly absent |
| API integration tests | Not written |

---

## Running tests locally (quick reference)

```bash
# Terminal 1 — backend
cd backend && mvn spring-boot:run

# Terminal 2 — frontend dev server
cd frontend && npm run dev

# Terminal 3 — run E2E tests
cd frontend && npm run test:e2e

# Or open Playwright UI (interactive runner)
cd frontend && npm run test:e2e:ui

# Unit tests
cd frontend && npm run test
```

---

## Playwright test selectors reference

Patterns that work reliably in this codebase:

| Target | Selector |
|---|---|
| Password input (first) | `page.locator('input[type="password"]').first()` |
| Add transaction button (toolbar) | `page.getByRole('button', { name: 'Add', exact: true })` |
| Amount sort button (table) | `page.getByRole('table').getByRole('button', { name: 'Amount' })` |
| Modal heading | `page.getByRole('heading', { name: 'Add Transaction' })` |
| Validation error paragraph | `dialog.locator('p').filter({ hasText: 'Select a category' })` |
| Success toast | `page.getByText(/Transaction (created\|saved)/i)` |
