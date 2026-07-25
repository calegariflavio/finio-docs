# UC-04 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/ForgotPasswordPage.tsx` | 2-step flow (email → code + new password) |
| `pages/settings/SecurityTab.tsx` | Change password form |
| `components/ui/PasswordChecklist.tsx` | Shared live-checklist component |

Both forms share `PasswordChecklist`. The checklist rules: ≥8 chars, uppercase, number, special character.

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.forgotPassword()`, `AuthController.resetPassword()`, `AuthController.changePassword()` |
| Service | `AuthService` |
| DTOs | `ResetPasswordRequest` (`@Pattern` on `newPassword`), `ChangePasswordRequest` |
| Entity columns | `User.resetPasswordToken`, `User.resetPasswordTokenExpiry` |

`@Pattern` is a Bean Validation annotation — invalid passwords return `400` before the service layer runs.

## API

```
POST /api/auth/forgot-password
Body: { email }
200: always (no enumeration)

POST /api/auth/reset-password
Body: { token, newPassword }
200: success
400: token invalid/expired or password fails validation

PUT /api/auth/change-password
Body: { currentPassword, newPassword }
200: success
400: current password wrong or new password invalid
```

## Key decisions

- Forgot-password always returns 200 to prevent email enumeration.
- `@Pattern` enforces strength server-side regardless of frontend checklist.
- Playwright test mocks `/api/auth/forgot-password` because SMTP is broken locally.
