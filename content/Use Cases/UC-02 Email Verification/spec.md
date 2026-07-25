---
uc: UC-02
name: Email Verification
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-02 — Email Verification

**Actor:** Newly registered user
**Entry point:** Verification link in email → `/verify-email?token=<token>`

---

## Main flow

### Verify email

1. User clicks the link in the verification email.
2. Frontend reads `?token` from the URL and calls `POST /api/auth/verify-email`.
3. Backend looks up the token, checks it isn't expired, sets `emailVerified = true`, clears the token.
4. Backend returns a JWT — user is now logged in.
5. Frontend stores the JWT and redirects to `/dashboard`.

### Resend verification

1. User attempts to log in with an unverified email.
2. Backend returns `400` with code `EMAIL_NOT_VERIFIED`.
3. Frontend shows the error + **"Resend verification email"** button.
4. Clicking calls `POST /api/auth/resend-verification`.
5. New token generated and emailed; success toast shown.

---

## Error states

| Condition | Response |
|---|---|
| Token invalid or not found | `400` |
| Token expired | `400` |
| Email already verified | `400` on resend |
| Email not found on resend | `404` |

---

## Constraints

- Token has a configurable expiry (set in `application.properties`).
- SMTP currently broken locally (expired Gmail app password) — see [[Known Issues]].
