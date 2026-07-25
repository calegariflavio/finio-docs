---
uc: UC-05
name: Profile & Preferences
area: Auth
plan: FREE
status: ✅ Complete
---

# UC-05 — Profile & Preferences

**Actor:** Authenticated user
**Entry point:** Settings → Profile tab / Preferences tab

---

## Main flow

### Update profile

1. Settings → Profile tab — full name and email pre-filled.
2. User edits and submits → `PUT /api/auth/profile`.
3. `AuthContext` user object refreshes with new values.

### Update preferences

1. Settings → Preferences tab.
2. User sets:
   - **Currency** — display symbol (CAD, USD, EUR, GBP, BRL, etc.)
   - **Theme** — light / dark / system
3. `PUT /api/auth/preferences`.
4. Theme applied immediately via `data-theme` on `<html>`.
5. Currency propagates to all money displays app-wide.

### Budget alert preferences

1. Same Preferences tab.
2. Toggle "Budget alerts" on/off.
3. Set alert threshold (%, e.g. 80).
4. Saved via `PUT /api/auth/preferences`.
5. Used by the scheduled `BudgetAlertService` — see [[UC-14 Budget Alerts]].

---

## Constraints

- Currency is display-only — amounts not converted, stored as-is.
- Email change does not reset `emailVerified`.
- No avatar/photo upload.
- Theme `system` reads `prefers-color-scheme` but does not react to OS changes at runtime.
