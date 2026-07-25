# UC-05 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/settings/ProfileTab.tsx` | Full name / email form |
| `pages/settings/PreferencesTab.tsx` | Currency, theme, budget alert settings |
| `context/AuthContext.tsx` | `updateUser()` merges new values into local state |

Theme is applied via a `useEffect` that sets `document.documentElement.setAttribute('data-theme', theme)` whenever the preference changes.

## Backend

| Layer | File |
|---|---|
| Controller | `AuthController.updateProfile()`, `AuthController.updatePreferences()` |
| Service | `AuthService.updateProfile()`, `AuthService.updatePreferences()` |
| Entity columns | `fullName`, `currency`, `theme`, `budgetAlertsEnabled`, `budgetAlertThreshold` |

## API

```
PUT /api/auth/profile
Body: { fullName, email }
200: { data: updatedUser }

PUT /api/auth/preferences
Body: { currency, theme, budgetAlertsEnabled, budgetAlertThreshold }
200: { data: updatedUser }
```
