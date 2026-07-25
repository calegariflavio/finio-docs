# UC-03 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/LoginPage.tsx` | Login form |
| `context/AuthContext.tsx` | Global auth state, token read/write |
| `components/layout/ProtectedRoute.tsx` | Redirects unauthenticated users to `/login` |
| `api/axiosInstance.ts` | Global 401/403 interceptor |

`AuthContext` initialises by reading the token from storage, then calling `/api/auth/me` to get the current user. All protected pages are wrapped in `ProtectedRoute`.

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.login()`, `AuthController.me()` |
| Service | `AuthService.login()` |
| Filter | `JwtAuthenticationFilter` — validates `Authorization: Bearer` on every request |
| Utility | `JwtUtil` — sign/verify JWTs |

Rate limiting: Bucket4j, 10 req/min per IP on `/api/auth/login`.

## API

```
POST /api/auth/login
Body: { email, password }
200: { data: { token, user: { id, email, fullName, plan, role, currency, theme } } }
400: EMAIL_NOT_VERIFIED
401: "Invalid email or password"

GET /api/auth/me
Headers: Authorization: Bearer <token>
200: { data: User }
401: token invalid/expired
```

## Key decisions

- `rememberMe` controls storage location (`localStorage` vs `sessionStorage`) — same JWT TTL either way.
- The 401 interceptor guard (`pathname !== '/login'`) was added to fix a bug where the login page's own API call triggered a page reload before the error could render. See commit `40d08e6`.
