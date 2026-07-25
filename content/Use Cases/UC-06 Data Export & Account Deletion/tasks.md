# UC-06 — Tasks

- [x] `AuthController.exportData()` — aggregate + serialize all user data to JSON
- [x] `AuthService.exportData()` — collect from all repositories
- [x] `AuthController.deleteAccount()` + `AuthService.deleteAccount()`
- [x] Cascade delete config on `User` entity (all `@OneToMany` use `CascadeType.ALL`)
- [x] `DataTab.tsx` — export button + delete account section
- [x] Confirmation modal — user must type their email to enable the delete button
- [x] Post-delete: clear auth state + redirect to `/login`
