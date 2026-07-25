# Use Cases — Index

*All use cases derived from the current Finio codebase. Status reflects what is built as of 2026-07-25.*

| UC | Name | Area | Plan | Status |
|---|---|---|---|---|
| [[UC-01 User Registration]] | User Registration | Auth | FREE | ✅ Complete |
| [[UC-02 Email Verification]] | Email Verification | Auth | FREE | ✅ Complete |
| [[UC-03 Authentication]] | Login & Session Management | Auth | FREE | ✅ Complete |
| [[UC-04 Password Management]] | Forgot Password & Change Password | Auth | FREE | ✅ Complete |
| [[UC-05 Profile & Preferences]] | Profile & Preferences | Auth | FREE | ✅ Complete |
| [[UC-06 Data Export & Account Deletion]] | GDPR Data Export & Account Deletion | Auth | FREE | ✅ Complete |
| [[UC-07 Categories]] | Category Management | Finance | FREE | ✅ Complete |
| [[UC-08 Transactions — CRUD]] | Transaction Create, Read, Update, Delete | Finance | FREE | ✅ Complete |
| [[UC-09 Transactions — Filtering & Search]] | Transaction Filtering & Search | Finance | FREE | ✅ Complete |
| [[UC-10 Transactions — Analytics]] | Summary, Calendar, Trends | Finance | FREE | ✅ Complete |
| [[UC-11 CSV Export & Import]] | CSV Export (FREE) and Import (PREMIUM) | Finance | FREE/PREMIUM | ✅ Complete |
| [[UC-12 Dashboard]] | Dashboard Stat Cards | Finance | FREE | ✅ Complete |
| [[UC-13 Budgets]] | Budget CRUD & Spending Tracking | Finance | FREE (3 limit) | ✅ Complete |
| [[UC-14 Budget Alerts]] | Automated Budget Alert Emails | Finance | FREE | ✅ Complete |
| [[UC-15 Goals]] | Savings Goals | Finance | PREMIUM | ✅ Complete |
| [[UC-16 Recurring Transactions]] | Recurring Transaction Templates | Finance | PREMIUM | ✅ Complete |
| [[UC-17 Net Worth]] | Net Worth Items & History | Finance | PREMIUM | ✅ Complete |
| [[UC-18 Reports]] | Monthly Financial Reports | Finance | FREE | ✅ Complete |
| [[UC-19 Stripe Subscription]] | Upgrade, Webhook, Cancel | Payments | — | ✅ Complete |
| [[UC-20 Admin Panel]] | Platform Admin Tools | Admin | ADMIN role | ✅ Complete |

---

## Dependency map

```
UC-01 (Register)
  └─► UC-02 (Verify Email)
        └─► UC-03 (Login)
              ├─► UC-04 (Password)
              ├─► UC-05 (Profile)
              ├─► UC-06 (Export/Delete)
              ├─► UC-07 (Categories)
              │     └─► UC-08 (Transactions CRUD)
              │           ├─► UC-09 (Filtering)
              │           ├─► UC-10 (Analytics)
              │           ├─► UC-11 (CSV)
              │           └─► UC-12 (Dashboard)
              ├─► UC-13 (Budgets)
              │     └─► UC-14 (Budget Alerts — scheduled)
              ├─► UC-15 (Goals) [PREMIUM]
              ├─► UC-16 (Recurring) [PREMIUM]
              ├─► UC-17 (Net Worth) [PREMIUM]
              ├─► UC-18 (Reports — scheduled)
              └─► UC-19 (Stripe)
                    └─► unlocks UC-15, UC-16, UC-17, UC-11 (import)

UC-20 (Admin) — parallel, requires ROLE_ADMIN
```
