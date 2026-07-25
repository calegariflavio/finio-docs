---
uc: UC-04
name: Password Management
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-04 — Forgot Password & Change Password

**Actor:** Any registered user
**Entry point:** `/forgot-password` (unauthenticated) · Settings → Security tab (authenticated)

---

## Main flow

### Forgot password

1. User navigates to `/forgot-password`.
2. **Step 1** — enters email → `POST /api/auth/forgot-password`.
   - Backend generates reset token, stores it on user, emails a reset code.
   - Always responds 200 regardless of whether email exists (no user enumeration).
3. **Step 2** — enters reset code + new password.
   - Live password-strength checklist on new password field.
   - `POST /api/auth/reset-password` with `{ token, newPassword }`.
   - Backend validates token expiry and `@Pattern` on password.
4. Frontend redirects to `/login` with success message.

### Change password (authenticated)

1. Settings → Security tab.
2. User enters current password + new password (with live checklist).
3. `PUT /api/auth/change-password`.
4. Backend verifies current password, hashes and saves new password.
5. Success toast. JWT remains valid (no forced re-login).

---

## Error states

| Condition | Response |
|---|---|
| Reset token invalid/expired | `400` |
| Password fails strength rules | `400` (Bean Validation) |
| Wrong current password on change | `400` |

---

## Constraints

- No password history check — previous passwords can be reused.
- Session is not invalidated after password change.
- Forgot-password email broken locally (SMTP expired) — test mocked. See [[Known Issues]].
