# UC-19 — Tasks

- [x] Add `stripeCustomerId` + `stripeSubscriptionId` columns to `users` (migration)
- [x] `StripeController` — `/checkout`, `/cancel`, `/webhook` endpoints
- [x] `StripeService.createCheckoutSession()` — Stripe SDK call
- [x] `StripeService.cancelSubscription()` — `cancel_at_period_end = true`
- [x] `StripeService.handleWebhookEvent()` — signature verify + dispatch
- [x] `UserService.upgradeToPremium()` + `UserService.downgradeToFree()`
- [x] Webhook endpoint added to public endpoints list in `SecurityConfig`
- [x] `SubscriptionTab.tsx` — plan status, upgrade CTA, cancel button
- [x] `PremiumGate.tsx` — blur + upgrade CTA for all PREMIUM-gated features
- [x] Stripe env vars: `STRIPE_SECRET_KEY`, `STRIPE_PRICE_ID`, `STRIPE_WEBHOOK_SECRET`
