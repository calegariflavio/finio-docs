# Finio — Known Issues & Technical Debt

*Last updated: 2026-07-28*

---

## Active issues

### SMTP — Gmail app password expired (local dev)
- **What:** The Gmail app password stored in `application-local.yml` has expired. Sending emails (verification, OTP, budget alerts) fails with 535 auth error in local dev.
- **Impact:** Forgot-password and register flows fail locally. Production unaffected as long as a fresh app password is set in the prod env var.
- **Fix:** Generate a new Google App Password for the Gmail account, update `application-local.yml`.
- **Workaround for tests:** The `playwright@finio.dev` test user was inserted directly into the DB with a pre-verified email to bypass this.

### Forgot-password E2E test requires API mock
- **What:** The Playwright test `forgot password step 2 shows checklist on new password field` uses `page.route()` to intercept `/api/auth/forgot-password` because the backend returns 500 when SMTP is down.
- **Impact:** The test passes but doesn't exercise the real API call end-to-end. If the forgot-password backend logic changes, the test won't catch it.
- **Fix:** Fix the SMTP issue and remove the `page.route()` mock.

### Stripe price ID hardcoded in application.yml
- **What:** `stripe.premium-price-id=price_1Tdi3KJwgr92nN6mg1qX56yL` is in `application.yml` (not behind an env var).
- **Impact:** If the Stripe price is ever recreated or a test/live switch is made, the app needs a rebuild.
- **Fix:** Move to an env var: `${STRIPE_PREMIUM_PRICE_ID}`.

---

## Resolved issues (reference)

### Security hardening (fixed 2026-07-28)
- **Actuator health details:** `show-details: when-authorized` — unauthenticated callers now receive `{"status":"UP"}` only.
- **Swagger gated:** `/swagger-ui/**` and `/api-docs/**` now require `ROLE_ADMIN` instead of `permitAll`.
- **JWT moved to HttpOnly cookie:** `auth-token` cookie (`HttpOnly; SameSite=Strict`) replaces `localStorage`/`sessionStorage`. Frontend uses `withCredentials: true`. Logout clears the cookie server-side. `JwtAuthenticationFilter` still accepts `Authorization: Bearer` as fallback for API clients.
- **Legal pages updated** to reflect cookie-based auth (effective date: 2026-07-28).
- **Commits:** `fb9bfdd`, `495127f`, `92b9c92`, `dd933ea`

### 401 interceptor cleared login error (fixed 2026-07-25)
- **What:** `axiosInstance` was intercepting every 401 response and redirecting to `/login`. When the login endpoint itself returned 401 (wrong password), the page reloaded before the error state could be set — so the "Invalid email or password" message never appeared.
- **Fix:** Added `&& window.location.pathname !== '/login'` guard to the 401 redirect.
- **Commit:** `40d08e6`

### Mobile navigation didn't work (fixed in prev session)
- **What:** The hamburger button toggled `collapsed` state (intended for the sidebar collapse on desktop) but didn't show the sidebar on mobile — it was always `hidden lg:flex`.
- **Fix:** Added separate `mobileOpen` state driving a backdrop + fixed-position drawer overlay on mobile.

---

## Technical debt

| Item | Severity | Notes |
|---|---|---|
| `userRepository.findByEmail()` called twice per request in most controllers | Low | Once in the filter chain, once in the controller to get the UUID. Could be injected via a `CurrentUser` resolved argument. Not a correctness issue, just an extra query. |
| No request-level caching | Low | Each controller method re-fetches the `User` entity. Inconsequential at current scale. |
| `application.yml` hardcodes Gmail SMTP username | Low | Should be an env var for clean environment separation. |
| No OpenTelemetry / distributed tracing | Low | Spring Actuator + logs are the only observability. Fine for MVP. |
| Budget alert emails don't include a link to the app | Low | Email just says "you're at X% of your budget." No CTA to open the app. |
| MonthlyReport generated even if user has no transactions | Low | Creates an empty report. Harmless but noisy. |
| No test coverage on backend services | Medium | Unit tests exist for some areas but service layer is largely covered only through integration-style testing. |
| `tsconfig.app.tsbuildinfo` committed | Trivial | Should be in `.gitignore`. Rebuilds it every time the TypeScript compiler runs. |

---

## Security notes

### Production deployment checklist
- Set `COOKIE_SECURE=true` so the `auth-token` cookie is only sent over HTTPS.
- No Content-Security-Policy header is configured — adding one would harden against XSS vectors.
- JWT has no revocation mechanism. Password change invalidates old tokens via `passwordChangedAt`, but logout relies on cookie expiry (default 24 h).
- Run `mvn dependency:check` and `npm audit` before going live.

### Informational
- Rate limiting (Bucket4j) is applied per-IP. `AuthRateLimitFilter` trusts `X-Forwarded-For` only when the direct connection comes from a private/loopback address — correct for a single reverse proxy on the same host. Verify in multi-instance or multi-hop proxy setups.
- Stripe webhooks are signature-verified inside `StripeService`. The endpoint is `permitAll()` in Spring Security by design.
- Admin role is bootstrapped via `ADMIN_EMAIL` env var. There is no admin self-registration path.
- JWT secret must be a minimum of 256 bits (32+ random bytes). A short secret is vulnerable to brute-force.
