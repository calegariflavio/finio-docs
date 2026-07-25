---
uc: UC-03
name: Authentication
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-03 — Login & Session Management

**Actor:** Registered, email-verified user
**Entry point:** `/login`

---

## Main flow

### Login

1. User enters email + password; optionally checks "Remember me".
2. `POST /api/auth/login`.
3. If `emailVerified = false` → `400 EMAIL_NOT_VERIFIED` → frontend shows resend button.
4. On success, backend returns `{ token, user }`.
5. JWT stored in `localStorage` (remember me) or `sessionStorage` (session only).
6. `AuthContext` sets `user` and `isAuthenticated = true`.
7. Redirects to `/dashboard` (or originally requested route).

### Session rehydration

- On app load, `AuthContext` reads token from storage.
- Calls `GET /api/auth/me` to refresh the user object.
- Invalid/expired token → clear storage → redirect `/login`.

### Logout

1. User clicks "Log out" in sidebar.
2. Clear `localStorage` + `sessionStorage`.
3. `AuthContext` resets. Redirect to `/login`.

### 401 global handling

`axiosInstance` intercepts all `401` responses. If `pathname !== '/login'`, clears storage and redirects. The path guard prevents a redirect loop when the login endpoint itself returns `401`.

---

## Error states

| Condition | Response |
|---|---|
| Wrong password | `401` "Invalid email or password" |
| Email not verified | `400 EMAIL_NOT_VERIFIED` |
| Account not found | `401` (same message — no user enumeration) |

---

## Constraints

- JWT is stateless — logout only clears the client; no server-side revocation.
- No TOTP / 2FA (deferred — see [[Future Roadmap]]).
- No concurrent session limit.
