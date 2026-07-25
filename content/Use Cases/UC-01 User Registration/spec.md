---
uc: UC-01
name: User Registration
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-01 — User Registration

**Actor:** Unauthenticated visitor
**Entry point:** `/register`

---

## Main flow

1. User navigates to `/register`.
2. Fills in: full name, email, password.
3. Password-strength checklist provides live feedback (min 8 chars, uppercase, number, special char).
4. On submit, frontend calls `POST /api/auth/register`.
5. Backend creates the user with `plan = FREE`, `role = USER`, `emailVerified = false`.
6. Backend sends a verification email with a one-time token link.
7. Frontend redirects to `/verify-email` — user must verify before logging in ([[UC-02 Email Verification]]).

---

## Constraints

- No OAuth / social login.
- Email uniqueness enforced at DB constraint level only (no pre-submit check).
- Registration is always FREE; no invite-only or waitlist mode.

---

## Error states

| Condition | Response |
|---|---|
| Email already in use | `409 Conflict` |
| Blank fields / bad email format | `400` validation errors |
| Password too weak | `400` (Bean Validation `@Pattern`) |
