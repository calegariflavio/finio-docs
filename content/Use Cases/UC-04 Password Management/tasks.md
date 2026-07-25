# UC-04 — Tasks

- [x] Add `resetPasswordToken` + `resetPasswordTokenExpiry` columns (migration)
- [x] `AuthController.forgotPassword()` — generate token, send email, always 200
- [x] `AuthController.resetPassword()` — validate token, hash + save new password
- [x] `AuthController.changePassword()` — verify current password, save new
- [x] `ResetPasswordRequest` DTO with `@Pattern` validation
- [x] `ChangePasswordRequest` DTO with `@Pattern` validation
- [x] `EmailService.sendPasswordResetEmail()` — email template with reset code
- [x] `ForgotPasswordPage.tsx` — 2-step form (email step + code/password step)
- [x] `SecurityTab.tsx` — change password form within settings
- [x] `PasswordChecklist` component — live rules feedback, shared with register
- [x] Playwright mock for forgot-password (SMTP broken) in `password-validation.spec.ts`
