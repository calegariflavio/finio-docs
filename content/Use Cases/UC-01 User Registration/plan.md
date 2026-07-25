# UC-01 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/RegisterPage.tsx` | Form page |
| `components/ui/PasswordChecklist.tsx` | Live strength checklist (shared with UC-04) |

React Hook Form + Zod schema. On submit calls `authApi.register()`. Redirects to `/verify-email` on success.

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.register()` |
| Service | `AuthService.register()` |
| Entity | `User` |
| Migration | `V1__create_users_table.sql` |

`BCryptPasswordEncoder` hashes the password before persist. `EmailService.sendVerificationEmail()` fires immediately after user save.

## API

```
POST /api/auth/register
Body: { fullName, email, password }
200: { success: true, data: null, message: "Registration successful. Please check your email." }
400: validation errors
409: email already in use
```

## Key decisions

- `emailVerified = false` on creation — login is blocked until UC-02 completes.
- No JWT issued at registration — forces the verification step.
- Rate limiting on `/api/auth/**` via Bucket4j (10 req/min per IP).
