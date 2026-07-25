# UC-20 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/admin/AdminDashboardPage.tsx` | Stats overview |
| `pages/admin/AdminUsersPage.tsx` | User list, search, plan controls |
| `hooks/useAdminUsers.ts` | Paginated user list query |
| `hooks/useAdminStats.ts` | Platform stats query |

Sidebar admin link is conditionally rendered: `user.role === 'ADMIN'`. Non-admins navigating to `/admin` directly are redirected by `ProtectedRoute` (which checks both `isAuthenticated` and optionally `requiredRole`).

## Backend

| Layer | File |
|---|---|
| Controller | `AdminController` — all `/api/admin/**` |
| Service | `AdminService` |
| Security | `SecurityConfig` — `.requestMatchers("/api/admin/**").hasRole("ADMIN")` |

`UserSummary` DTO (not the full User entity) is returned in the user list to avoid leaking password hashes.

## API

```
GET /api/admin/stats
200: { totalUsers, totalTransactions, totalRevenue }  or  403

GET /api/admin/users?search=&page=0&size=20
200: { content: UserSummary[], totalElements }  or  403

PUT /api/admin/users/{id}/plan
Body: { plan: "FREE" | "PREMIUM" }
200: { data: updatedUser }  or  403, 404
```
