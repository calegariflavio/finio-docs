# UC-03 — Tasks

- [x] `JwtUtil` — sign/verify JWT, extract claims
- [x] `JwtAuthenticationFilter` — validate `Authorization: Bearer` on every request
- [x] `AuthController.login()` — verify credentials, check emailVerified, return token + user
- [x] `AuthController.me()` — return current user from security context
- [x] `AuthService.login()` — BCrypt compare, plan/role load
- [x] `SecurityConfig` — public endpoints list, filter chain
- [x] `LoginPage.tsx` — React Hook Form, remember me checkbox, error display
- [x] `AuthContext.tsx` — token storage, `useAuth()` hook, rehydration on mount
- [x] `ProtectedRoute.tsx` — redirect unauthenticated users
- [x] `axiosInstance.ts` — attach JWT to all requests, 401/403 interceptor
- [x] Fix: 401 interceptor path guard (`pathname !== '/login'`) — bug commit `40d08e6`
- [x] Rate limiting on `/api/auth/login` (Bucket4j)
