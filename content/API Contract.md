# Finio — API Contract

*Base URL: `/api` | Auth: `Authorization: Bearer <jwt>` on all non-public endpoints*
*Last updated: 2026-07-25*

Full interactive docs at http://localhost:8080/swagger-ui.html

---

## Auth  `/api/auth`

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/register` | Public | Register. Body: `{email, password, fullName}`. Returns 201. |
| POST | `/login` | Public | Login. Body: `{email, password}`. Returns `AuthResponse`. |
| GET | `/verify-email?token=` | Public | Verify email, returns JWT. |
| POST | `/resend-verification` | Public | Resend verification email. Body: `{email}`. |
| POST | `/forgot-password` | Public | Send OTP to email. Body: `{email}`. |
| POST | `/reset-password` | Public | Reset with OTP. Body: `{email, otp, newPassword}`. |
| GET | `/me` | JWT | Current user info. Returns `AuthResponse`. |
| PUT | `/profile` | JWT | Update display name. Body: `{fullName}`. |
| POST | `/password/request-otp` | JWT | Send OTP for password change to user's email. |
| PUT | `/password` | JWT | Change password. Body: `{currentPassword, newPassword, otp}`. |
| GET | `/me/export` | JWT | Export all personal data as JSON (GDPR). |
| DELETE | `/account` | JWT | Delete account + all data. Returns 204. |
| GET | `/preferences` | JWT | Get user preferences. |
| PUT | `/preferences` | JWT | Update preferences. Body: `{currency, theme, budgetAlertsEnabled, budgetAlertThreshold}`. |

### AuthResponse shape
```json
{
  "email": "user@example.com",
  "fullName": "Jane Doe",
  "plan": "FREE",
  "role": "USER",
  "emailVerified": true
}
```

---

## Transactions  `/api/transactions`

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/` | JWT | Create transaction. Body: `TransactionRequest`. |
| GET | `/` | JWT | List with filters + pagination. See filters below. |
| GET | `/{id}` | JWT | Get by ID. |
| PUT | `/{id}` | JWT | Update transaction. |
| DELETE | `/{id}` | JWT | Delete transaction. Returns 204. |
| DELETE | `/bulk` | JWT | Bulk delete. Body: `[uuid, ...]`. Returns 204. |
| GET | `/summary?month=&year=` | JWT | Monthly income/expense/balance totals. |
| GET | `/summary/range?months=6` | JWT | Last N months summary array. |
| GET | `/calendar?month=&year=` | JWT | All transactions for a month (calendar view). |
| GET | `/trends?months=6` | JWT | Category spending trends over N months. |
| GET | `/export` | JWT | Download all transactions as CSV file. |

### TransactionRequest
```json
{
  "categoryId": "uuid",
  "amount": 42.00,
  "type": "EXPENSE",
  "description": "Coffee",
  "date": "2026-07-25",
  "tags": ["work", "coffee"]
}
```

### GET /transactions filters (query params)
```
month, year          integer — monthly filter
startDate, endDate   date string — range filter (alternative to month/year)
type                 INCOME | EXPENSE
categoryId           UUID
search               string — description search
minAmount            decimal
maxAmount            decimal
tag                  string — single tag filter
page, size, sort     pagination (default: size=20, sort=date,desc)
```

---

## Categories  `/api/categories`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/` | JWT | List all (system + user's custom). |
| POST | `/` | JWT | Create custom category. Body: `{name, type, icon}`. |
| PUT | `/{id}` | JWT | Update custom category. |
| DELETE | `/{id}` | JWT | Delete custom category. |

---

## Budgets  `/api/budgets`

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/` | JWT | Create budget. Body: `{categoryId, limitAmount, month, year, alertThreshold?}`. FREE: max 3/month. |
| GET | `/?month=&year=` | JWT | List budgets with spending data. |
| GET | `/summary?month=&year=` | JWT | Totals + status counts. |
| PUT | `/{id}` | JWT | Update limit or alert threshold. |
| POST | `/copy-from-previous?targetMonth=&targetYear=` | JWT | Copy last month's budgets. |
| DELETE | `/{id}` | JWT | Delete budget. |

---

## Goals  `/api/goals`  *(PREMIUM)*

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/` | JWT | Create goal. Body: `{name, targetAmount, savedAmount, targetDate?}`. |
| GET | `/` | JWT | List all goals. |
| PUT | `/{id}` | JWT | Update goal. |
| DELETE | `/{id}` | JWT | Delete goal. |

---

## Recurring Transactions  `/api/recurring`  *(PREMIUM)*

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/` | JWT | Create template. Body: `{categoryId, amount, type, description?, frequency, startDate, endDate?}`. |
| GET | `/` | JWT | List all templates. |
| PUT | `/{id}` | JWT | Update template. |
| PATCH | `/{id}/toggle` | JWT | Pause or resume. |
| POST | `/{id}/trigger` | JWT | Fire now + advance next due date. |
| DELETE | `/{id}` | JWT | Delete template. |

---

## Net Worth  `/api/net-worth`  *(PREMIUM)*

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/` | JWT | List all items (assets + liabilities) with calculated totals. |
| GET | `/history` | JWT | Historical snapshots array. |
| POST | `/` | JWT | Add item. Body: `{name, type, amount, note?}`. |
| PUT | `/{id}` | JWT | Update item. |
| DELETE | `/{id}` | JWT | Delete item. |

---

## Reports  `/api/reports`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/?month=&year=` | JWT | Get monthly report. |

---

## CSV Import  `/api/import`  *(PREMIUM)*

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/csv/preview` | JWT | Preview CSV (multipart). Returns parsed rows + errors. |
| POST | `/csv` | JWT | Import CSV (multipart). Creates transactions. |
| GET | `/csv/template` | JWT | Download sample CSV template. |

---

## Stripe  `/api/stripe`

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/create-checkout-session` | JWT | Create Stripe Checkout session. Returns `{url}`. |
| POST | `/webhook` | Public (sig-verified) | Stripe webhook receiver. |
| POST | `/cancel` | JWT | Cancel subscription at period end. |
| GET | `/status` | JWT | Current subscription status. |

---

## Admin  `/api/admin`  *(ROLE_ADMIN)*

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/stats` | ADMIN | Platform stats (users, transactions, revenue). |
| GET | `/users?search=&page=&size=` | ADMIN | Paginated user list. |
| PATCH | `/users/{id}/plan` | ADMIN | Set plan. Body: `{plan: "FREE" | "PREMIUM"}`. |
| POST | `/trigger/monthly-reports` | ADMIN | Manually run monthly report generation. |
| POST | `/trigger/budget-alerts` | ADMIN | Manually run budget alert check. |

---

## Health  `/api/health`

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/` | Public | Health check. Returns `{status: "UP"}`. |

---

## Error shapes

### 422 — Plan upgrade required
```json
{
  "type": "about:blank",
  "title": "Upgrade Required",
  "status": 422,
  "detail": "This feature requires a Premium plan."
}
```

### 401 — Unauthorized
```json
{
  "type": "about:blank",
  "title": "Unauthorized",
  "status": 401,
  "detail": "Invalid email or password"
}
```

### 409 — Duplicate email
```json
{
  "type": "about:blank",
  "title": "Conflict",
  "status": 409,
  "detail": "Email already registered"
}
```
