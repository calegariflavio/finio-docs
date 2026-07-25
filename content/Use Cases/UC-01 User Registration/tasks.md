# UC-01 — Tasks

- [x] `V1__create_users_table.sql` migration (id, email, password_hash, full_name, plan, role, email_verified, ...)
- [x] `User` entity with `BaseEntity` (UUID id, createdAt, updatedAt)
- [x] `AuthController.register()` endpoint
- [x] `AuthService.register()` — BCrypt hash, save user, trigger verification email
- [x] `RegisterPage.tsx` — React Hook Form + Zod
- [x] `PasswordChecklist` component with live validation rules
- [x] `@Pattern` Bean Validation on password field
- [x] Redirect to `/verify-email` on success
- [x] `409` error handling for duplicate email on frontend
