---
uc: UC-06
name: Data Export & Account Deletion
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-06 — GDPR Data Export & Account Deletion

**Actor:** Authenticated user
**Entry point:** Settings → Data tab

---

## Main flow

### Data export

1. User clicks "Export my data".
2. `GET /api/auth/export`.
3. Backend aggregates: profile, transactions, categories, budgets, goals, recurring templates, net worth items.
4. Returns JSON file download (`Content-Disposition: attachment`).

### Account deletion

1. User clicks "Delete my account".
2. Confirmation modal — user must type their email to confirm.
3. `DELETE /api/auth/account`.
4. Backend hard-deletes user and all associated data (JPA cascade).
5. Frontend clears auth state → redirects to `/login`.

---

## Error states

| Condition | Response |
|---|---|
| Wrong confirmation email | Frontend blocks submit (client-side validation) |

---

## Constraints

- Export format is JSON only (CSV export of transactions is a separate flow — see [[UC-11 CSV Export & Import]]).
- Deletion is immediate and permanent — no soft delete, no grace period.
- Stripe subscription is **not** automatically cancelled on deletion (orphaned subscription risk).
