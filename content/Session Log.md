# Finio — Session Log

*Running log of significant work done per session. Newest first.*

---

## 2026-07-28

### OWASP Top 10 security review + fixes

Three security commits landed:

- **`fb9bfdd`** — Actuator health details restricted to authorized users (`show-details: when-authorized`). Swagger/OpenAPI gated behind `ROLE_ADMIN`.
- **`495127f`** — JWT moved from `localStorage`/`sessionStorage` to an `HttpOnly; SameSite=Strict` cookie (`auth-token`). `rememberMe=true` → 30-day Max-Age, unchecked → session cookie. New `POST /api/auth/logout` endpoint clears the cookie server-side. `JwtAuthenticationFilter` reads cookie first, falls back to `Authorization: Bearer` for API clients. Frontend updated: `withCredentials: true`, no token in storage, `AuthContext` no longer holds token state. Auth tests rewritten. Storage Policy page updated.
- **`92b9c92`** — Storage Policy effective date updated to 2026-07-28.

Remaining low-severity items (see [[Known Issues#Security notes]]):
- `COOKIE_SECURE=true` must be set in production.
- No Content-Security-Policy header configured.
- JWT has no revocation mechanism (password change invalidates old tokens, but logout doesn't revoke until expiry — mitigated by short expiry).
- Run `mvn dependency:check` and `npm audit` before prod deploy.

### Legal pages updated (effective date + cookie wording)
- Privacy Policy and Terms of Service effective dates bumped to July 28, 2026.
- Privacy Policy section 4 reworded: removed incorrect claim that tokens are stored in `localStorage`/`sessionStorage`; now correctly describes HttpOnly cookie auth.
- **Commit:** `dd933ea`

---

## 2026-07-25

### Playwright E2E test suite (20 tests, all passing)
- Created `frontend/playwright.config.ts` and `frontend/e2e/` with 4 spec files.
- Inserted `playwright@finio.dev` test user directly into DB (SMTP was broken).
- 3 debugging iterations to fix selector mismatches (heading text, button names, error text).
- Fixed real bug: `axiosInstance` 401 interceptor was reloading the login page before the "wrong password" error could render. Fix: skip redirect if `pathname === '/login'`.

### Password validation checklists
- Added live password-strength checklist to forgot-password step 2 and settings change-password form.
- Added `@Pattern` Bean Validation on `ResetPasswordRequest.newPassword` and `ChangePasswordRequest.newPassword`.

### Admin panel
- Built admin user list with search + pagination.
- Admin can upgrade or downgrade any user's plan (FREE ↔ PREMIUM).
- Platform stats endpoint (total users, transactions, revenue).

### Mobile navigation fix
- Hamburger was toggling sidebar `collapsed` state which did nothing on mobile (`hidden lg:flex`).
- Fixed with separate `mobileOpen` state driving a backdrop + fixed-position drawer overlay.

### Email verification
- Full email verification flow: register → email sent → click link → `emailVerified = true` → JWT returned.
- Resend verification endpoint.
- Login blocked until email is verified (returns `EMAIL_NOT_VERIFIED` error code, frontend shows resend button).

---

## Earlier sessions (pre-log)

- Initial project scaffold (Spring Boot + React)
- Auth: register, login, forgot-password, change-password
- Transactions: CRUD, filters, pagination, bulk delete, tags, export CSV
- Budgets: CRUD, spending data, copy from previous month, budget alerts via email
- Goals, Recurring Transactions, Net Worth (all PREMIUM)
- Stripe integration: Checkout, webhook, subscription management
- Dashboard: income/expense/balance stat cards, charts
- Calendar view
- Reports: monthly income/expense chart, category breakdown
- CSV import (PREMIUM)
- Settings page: profile, security, preferences, subscription, data
- GDPR data export + account deletion
- Rate limiting (Bucket4j) on auth endpoints
- Scheduled jobs: monthly reports, budget alerts, recurring transactions, net worth snapshots
- Flyway migrations V1 → V20
- Vitest unit tests for AuthContext and PremiumGate
