# UC-02 — Tasks

- [x] Add `emailVerificationToken` and `emailVerificationTokenExpiry` columns to `users` table (migration)
- [x] `AuthService.register()` generates token and calls `EmailService.sendVerificationEmail()`
- [x] `AuthController.verifyEmail()` + `AuthService.verifyEmail()`
- [x] `AuthController.resendVerification()` + `AuthService.resendVerificationEmail()`
- [x] `VerifyEmailPage.tsx` — reads `?token`, calls API, handles success/error states
- [x] `LoginPage.tsx` — detects `EMAIL_NOT_VERIFIED` error code, shows resend button
- [x] `EmailService.sendVerificationEmail()` — HTML email template with link
