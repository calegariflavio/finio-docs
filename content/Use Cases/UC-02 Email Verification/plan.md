# UC-02 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/VerifyEmailPage.tsx` | Reads `?token`, calls API, shows result/error |
| `pages/LoginPage.tsx` | Shows resend button when `EMAIL_NOT_VERIFIED` error returned |

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.verifyEmail()`, `AuthController.resendVerification()` |
| Service | `AuthService.verifyEmail()`, `AuthService.resendVerificationEmail()` |
| Entity columns | `User.emailVerificationToken`, `User.emailVerificationTokenExpiry` |

Token stored as a plain UUID string on the `User` row. Expiry is a `LocalDateTime` column.

## API

```
POST /api/auth/verify-email
Body: { token }
200: { data: { token, user } }   ← JWT issued immediately
400: token invalid or expired

POST /api/auth/resend-verification
Body: { email }
200: { success: true }
400: already verified
404: email not found
```

## Key decisions

- JWT is issued on successful verification (no separate login step required).
- Token is cleared from the DB after use (single-use).
- Resend regenerates a new token (old one is invalidated).
