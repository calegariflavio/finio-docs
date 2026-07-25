---
uc: UC-20
name: Admin Panel
area: Admin
plan: ROLE_ADMIN only
status: ✅ Complete
---

# UC-20 — Platform Admin Tools

**Actor:** User with `role = ADMIN`
**Entry point:** `/admin`

---

## Main flow

### Platform stats

- `GET /api/admin/stats` → stat cards: total users, total transactions, total revenue.

### User list

- `GET /api/admin/users?search=&page=0&size=20`.
- Table: email, full name, plan, role, email verified, joined date.
- Search by email or name. Paginated.

### Upgrade / downgrade plan

1. Admin finds a user.
2. Clicks "Upgrade to PREMIUM" or "Downgrade to FREE".
3. Confirmation prompt.
4. `PUT /api/admin/users/{id}/plan` with `{ plan }`.
5. Backend updates `user.plan` directly (bypasses Stripe).

---

## Constraints

- Requires `role = ADMIN` — enforced in `SecurityConfig` (`/api/admin/**` requires `ROLE_ADMIN`).
- Admin sidebar link only visible when `user.role === 'ADMIN'`.
- No audit log of admin actions.
- Plan change bypasses Stripe — active subscription not affected.
- Admin user must be seeded directly in DB (no UI to promote to ADMIN).
- No user impersonation (deferred — see [[Future Roadmap]]).
