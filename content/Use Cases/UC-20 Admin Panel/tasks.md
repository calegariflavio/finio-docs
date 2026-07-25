# UC-20 — Tasks

- [x] `SecurityConfig` — `/api/admin/**` requires `ROLE_ADMIN`
- [x] `AdminController` — stats, user list, plan update endpoints
- [x] `AdminService` — platform stats aggregation, user search + pagination
- [x] `UserSummary` DTO (no password hash)
- [x] `PUT /api/admin/users/{id}/plan` — direct plan override
- [x] `AdminDashboardPage.tsx` — stats cards
- [x] `AdminUsersPage.tsx` — user table, search, upgrade/downgrade buttons
- [x] `useAdminUsers` + `useAdminStats` hooks
- [x] Sidebar admin link conditional on `user.role === 'ADMIN'`
- [x] `ProtectedRoute` role guard for `/admin/*` routes
