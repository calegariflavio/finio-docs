# Finio — Domain Model

*Last updated: 2026-07-25*

---

## Entity overview

```
User
 ├── Transaction (n)
 ├── Category (n, nullable user_id = system category)
 ├── Budget (n)
 ├── Goal (n)
 ├── RecurringTransaction (n)
 ├── NetWorthItem (n)
 ├── NetWorthSnapshot (n)
 └── MonthlyReport (n)
```

All entities extend `BaseEntity` which provides:
- `id` (UUID, auto-generated)
- `createdAt` (Instant, JPA auditing)
- `updatedAt` (Instant, JPA auditing)

---

## User

```
users
  id                              UUID PK
  email                           VARCHAR UNIQUE NOT NULL
  password_hash                   VARCHAR NOT NULL
  full_name                       VARCHAR NOT NULL
  plan                            ENUM(FREE, PREMIUM) DEFAULT FREE
  role                            ENUM(USER, ADMIN) DEFAULT USER
  stripe_customer_id              VARCHAR
  stripe_subscription_id          VARCHAR
  plan_expires_at                 TIMESTAMP
  budget_alerts_enabled           BOOLEAN DEFAULT TRUE
  budget_alert_threshold          INT DEFAULT 80
  currency                        VARCHAR(10) DEFAULT 'CAD'
  theme                           VARCHAR(20) DEFAULT 'system'
  email_verified                  BOOLEAN DEFAULT FALSE
  email_verification_token        VARCHAR
  email_verification_token_exp    TIMESTAMP
  password_otp_hash               VARCHAR
  password_otp_expires_at         TIMESTAMP
  password_changed_at             TIMESTAMP
  created_at                      TIMESTAMP NOT NULL
  updated_at                      TIMESTAMP NOT NULL
```

---

## Transaction

```
transactions
  id              UUID PK
  user_id         UUID FK → users
  category_id     UUID FK → categories
  amount          DECIMAL(19,2) NOT NULL
  type            ENUM(INCOME, EXPENSE) NOT NULL
  description     TEXT
  date            DATE NOT NULL
  source          ENUM(MANUAL, CSV_IMPORT) DEFAULT MANUAL
  created_at      TIMESTAMP
  updated_at      TIMESTAMP

transaction_tags (join table)
  transaction_id  UUID FK → transactions
  tag             VARCHAR(50)
```

---

## Category

```
categories
  id       UUID PK
  name     VARCHAR(100) NOT NULL
  type     ENUM(INCOME, EXPENSE) NOT NULL
  icon     VARCHAR(50)
  user_id  UUID FK → users (NULL = system/global category)
  created_at / updated_at
```

System categories (`user_id IS NULL`) are seeded by Flyway `V7__seed_default_categories.sql` and visible to all users. Users can create custom categories (linked to their `user_id`). Custom categories are only visible to their creator.

---

## Budget

```
budgets
  id              UUID PK
  user_id         UUID FK → users
  category_id     UUID FK → categories
  limit_amount    DECIMAL(19,2) NOT NULL
  month           INT NOT NULL
  year            INT NOT NULL
  alert_threshold INT (NULL = use user's global threshold)
  created_at / updated_at
```

FREE plan: max 3 budgets per month/year. PREMIUM: unlimited.

---

## Goal

```
goals
  id             UUID PK
  user_id        UUID FK → users
  name           VARCHAR(255) NOT NULL
  target_amount  DECIMAL(19,2) NOT NULL
  saved_amount   DECIMAL(19,2) DEFAULT 0
  target_date    DATE (optional)
  created_at / updated_at
```

PREMIUM only. Goals are manually updated (no automatic deduction from transactions).

---

## RecurringTransaction

```
recurring_transactions
  id            UUID PK
  user_id       UUID FK → users
  category_id   UUID FK → categories
  amount        DECIMAL(19,2) NOT NULL
  type          ENUM(INCOME, EXPENSE) NOT NULL
  description   TEXT
  frequency     ENUM(DAILY, WEEKLY, MONTHLY, YEARLY) NOT NULL
  start_date    DATE NOT NULL
  next_due_date DATE NOT NULL
  end_date      DATE (optional)
  active        BOOLEAN DEFAULT TRUE
  created_at / updated_at
```

PREMIUM only. The scheduler processes due recurring transactions daily at midnight, creating actual `Transaction` records and advancing `next_due_date`.

---

## NetWorthItem

```
net_worth_items
  id         UUID PK
  user_id    UUID FK → users
  name       VARCHAR(100) NOT NULL
  type       ENUM(ASSET, LIABILITY) NOT NULL
  amount     DECIMAL(19,2) NOT NULL
  note       TEXT
  created_at / updated_at
```

PREMIUM only.

---

## NetWorthSnapshot

```
net_worth_snapshots
  id              UUID PK
  user_id         UUID FK → users
  total_assets    DECIMAL(19,2)
  total_liabilities DECIMAL(19,2)
  net_worth       DECIMAL(19,2)
  recorded_at     TIMESTAMP
  created_at / updated_at
```

Auto-recorded by the scheduler on the last day of each month.

---

## MonthlyReport

```
monthly_reports
  id              UUID PK
  user_id         UUID FK → users
  month           INT
  year            INT
  total_income    DECIMAL(19,2)
  total_expenses  DECIMAL(19,2)
  net_balance     DECIMAL(19,2)
  created_at / updated_at
```

Auto-generated on the 1st of each month for the previous month.

---

## Enums

| Enum | Values |
|---|---|
| `Plan` | `FREE`, `PREMIUM` |
| `Role` | `USER`, `ADMIN` |
| `TransactionType` | `INCOME`, `EXPENSE` |
| `TransactionSource` | `MANUAL`, `CSV_IMPORT` |
| `RecurringFrequency` | `DAILY`, `WEEKLY`, `MONTHLY`, `YEARLY` |
| `NetWorthType` | `ASSET`, `LIABILITY` |
| `BudgetStatus` | `ON_TRACK`, `WARNING`, `EXCEEDED` |

---

## Flyway migration history

| Version | Description |
|---|---|
| V1 | Schema init |
| V2 | Create users |
| V3 | Create categories |
| V4 | Create transactions |
| V5 | Create budgets |
| V6 | Create monthly_reports |
| V7 | Seed default categories |
| V8 | Add Stripe fields to users |
| V9 | Add user preferences (currency, theme, budget alerts) |
| V10 | Create recurring_transactions |
| V11 | Create goals |
| V12 | Create net_worth_items |
| V13 | Add transaction tags |
| V14 | Add role to users |
| V15 | Add email verification + OTP fields |
| V16 | Add budget alert threshold + user theme |
| V17 | Create net_worth_snapshots |
| V18 | Add performance indexes |
| V19 | Add password_changed_at |
| V20 | Create stripe_webhook_events (idempotency) |
