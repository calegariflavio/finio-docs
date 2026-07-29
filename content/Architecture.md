# Finio — Architecture

*Last updated: 2026-07-28*

---

## Backend — Spring Boot

### Layer structure

```
controller  →  service  →  repository  →  entity
                  ↓
               mapper (MapStruct)  →  DTO
```

Every controller endpoint resolves the authenticated user from `@AuthenticationPrincipal UserDetails` and calls `userRepository.findByEmail(email)` to get the UUID. All business logic is in the service layer.

### API response wrapper

Every response body is `ApiResponse<T>`:

```json
{
  "success": true,
  "data": { ... },
  "message": "OK",
  "timestamp": "2026-07-25T12:00:00Z"
}
```

Errors use RFC 7807 `ProblemDetail` via `GlobalExceptionHandler`. Common error codes:
- `401 Unauthorized` — missing/invalid JWT
- `403 Forbidden` — role insufficient (e.g. non-admin hitting `/api/admin/**`)
- `404 Not Found` — resource doesn't exist or belongs to another user
- `409 Conflict` — duplicate email on register
- `422 Unprocessable Entity` — plan upgrade required (free user hitting premium feature)

### Auth flow

1. `POST /api/auth/register` → creates user, sends verification email, returns 201.
2. User clicks link → `GET /api/auth/verify-email?token=...` → marks `emailVerified = true`, sets `auth-token` cookie, returns user data.
3. `POST /api/auth/login` → validates credentials + `emailVerified`, sets `auth-token` cookie, returns user data (no token in body).
4. All subsequent requests: browser sends `auth-token` cookie automatically → `JwtAuthenticationFilter` reads it and sets `SecurityContext`.
5. `POST /api/auth/logout` → server clears the `auth-token` cookie (Max-Age=0).

The `auth-token` cookie is `HttpOnly; SameSite=Strict`. `rememberMe=true` sets `Max-Age=30d`; unchecked gives a session cookie. `JwtAuthenticationFilter` also accepts `Authorization: Bearer <token>` as a fallback for API/mobile clients.

> **Production requirement:** Set `COOKIE_SECURE=true` so the `auth-token` cookie is transmitted over HTTPS only. Default is `false` for local dev.

JWT expiry is configurable via `JWT_EXPIRATION_MS` env var (default 24 h).

### Plan system

`User.plan` is `FREE | PREMIUM` (Java enum `Plan`).

Services check `user.getPlan() != Plan.PREMIUM` and throw `PlanUpgradeRequiredException` (→ 422) when free users hit gated features. See [[Feature Inventory]] for the full gate list.

Stripe checkout → webhook (`checkout.session.completed`) → `UserService.upgradeToPremium()`. Cancellation webhook (`customer.subscription.deleted`) → `UserService.downgradeToFree()`.

### Scheduled jobs

| Schedule | Job |
|---|---|
| 1st of every month, 01:00 | Generate monthly reports for all users |
| Daily 09:00 | Budget alert emails (for budgets ≥ threshold % spent) |
| Daily 00:00 | Process due recurring transactions |
| Last day of month, 23:00 | Record net worth snapshot for all users |

### Security headers

HSTS, `X-Content-Type-Options`, `Referrer-Policy: strict-origin-when-cross-origin`, and `Permissions-Policy` (no camera/mic/geo/payment) are set on every response. `/actuator/health` returns only `{"status":"UP"}` to unauthenticated callers (`show-details: when-authorized`).

### Public endpoints (no JWT required)

```
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/verify-email
POST /api/auth/resend-verification
POST /api/auth/forgot-password
POST /api/auth/reset-password
GET  /api/health
POST /api/stripe/webhook          ← signature-verified inside handler
/actuator/health                  ← status only; full details require ADMIN
```

`/actuator/**` (other than `/health`), `/api/admin/**`, `/swagger-ui/**`, and `/api-docs/**` require `ROLE_ADMIN`.

---

## Frontend — React + Vite

### File structure

```
src/
├── api/          axiosInstance.ts, authApi.ts, financeApi.ts
├── components/
│   ├── layout/   AppLayout.tsx, Sidebar.tsx, AppHeader.tsx
│   ├── forms/    TransactionForm.tsx, BudgetForm.tsx, ...
│   ├── modals/   TransactionModal.tsx, BudgetModal.tsx, ...
│   └── ui/       shadcn primitives (Button, Input, Dialog, ...)
├── context/      AuthContext.tsx, PreferencesContext.tsx
├── hooks/        useTransactions.ts, useBudgets.ts, ...
├── pages/        One file per route
├── router/       index.tsx, ProtectedRoute.tsx
├── types/        auth.ts, finance.ts
└── lib/          utils.ts, format.ts
```

### API layer

`axiosInstance.ts` uses `withCredentials: true` so the browser sends the `auth-token` HttpOnly cookie on every request automatically. No token is stored in `localStorage` or `sessionStorage`. Global response interceptor:
- `401` on a non-login endpoint → redirect to `/login`.
- `403` → redirect to `/forbidden`.

`financeApi.ts` and `authApi.ts` wrap every call in `unwrap()` which extracts `.data.data` from the `ApiResponse` envelope, so hooks receive the actual payload directly.

### Auth state

`AuthContext` / `useAuth()` holds `{ user, isAuthenticated }`. On mount it calls `GET /api/auth/me` — if the `auth-token` cookie is valid the user is hydrated; if not, the user stays `null`. `ProtectedRoute` wraps all authenticated pages — unauthenticated users are redirected to `/login`.

> **No token in JS:** The JWT lives exclusively in the HttpOnly cookie. No token is held in React state, `localStorage`, or `sessionStorage` — it is invisible to JavaScript entirely.

### Data fetching

React Query hooks in `hooks/`. `staleTime` is set to `0` (refetch on every page mount) so data is always fresh after navigating away and back. Mutations live in separate `use*Mutations.ts` files and invalidate the relevant query keys on success.

### Plan gating (frontend)

`<PremiumGate feature="...">` blurs children and shows an upgrade CTA for free users. Checks `user.plan === UserPlan.PREMIUM` from `useAuth()`. The backend enforces the same check — the frontend gate is UX, not security.

### Path alias

`@` maps to `frontend/src/`. Vite proxies `/api/*` to `:8080` in dev.

### Routing

```
/                     → redirect to /dashboard
/login                → LoginPage
/register             → RegisterPage
/verify-email-sent    → VerifyEmailSentPage
/forgot-password      → ForgotPasswordPage
/verify-email         → VerifyEmailPage (token param)
/forbidden            → ForbiddenPage

[ProtectedRoute + AppLayout]
  /dashboard          → DashboardPage
  /transactions       → TransactionsPage
  /budgets            → BudgetsPage
  /goals              → GoalsPage
  /recurring          → RecurringPage
  /net-worth          → NetWorthPage
  /calendar           → CalendarPage
  /reports            → ReportsPage
  /import             → ImportPage (CSV)
  /upgrade            → UpgradePage
  /upgrade/success    → UpgradeSuccessPage
  /upgrade/cancel     → UpgradeCancelPage
  /settings           → SettingsPage
  /admin              → AdminPage
  *                   → NotFound
```
