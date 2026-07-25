# UC-06 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/settings/DataTab.tsx` | Export button + delete account with confirmation modal |

Confirmation modal requires the user to type their email before the delete button is enabled.

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.exportData()`, `AuthController.deleteAccount()` |
| Service | `AuthService.exportData()`, `AuthService.deleteAccount()` |

`exportData()` calls each domain repository to collect all records for the current user, then serializes to a `Map<String, Object>` → Jackson JSON.

Cascade delete: `User` entity has `CascadeType.ALL` + `orphanRemoval = true` on all `@OneToMany` relationships.

## API

```
GET /api/auth/export
200: application/json file download (all user data)

DELETE /api/auth/account
200: success
```
