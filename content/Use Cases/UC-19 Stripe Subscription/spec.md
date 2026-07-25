---
uc: UC-19
name: Stripe Subscription
area: Payments
plan: —
status: ✅ Complete
---

# UC-19 — Stripe Subscription (Upgrade, Webhook, Cancel)

**Actor:** FREE user (upgrade) · PREMIUM user (cancel)
**Entry point:** Settings → Subscription tab · `PremiumGate` upgrade CTA

---

## Main flow

### Upgrade (FREE → PREMIUM)

1. User clicks "Upgrade to PREMIUM".
2. `POST /api/stripe/checkout` → backend creates a Stripe Checkout Session.
3. Frontend redirects to Stripe-hosted checkout page.
4. User pays on Stripe.
5. Stripe fires `checkout.session.completed` webhook → `POST /api/stripe/webhook`.
6. Backend verifies Stripe signature → `UserService.upgradeToPremium(userId)` — sets `plan = PREMIUM`, stores `stripeCustomerId` + `stripeSubscriptionId`.
7. On next app load user sees PREMIUM features unlocked.

### Cancel (PREMIUM → FREE)

1. Settings → Subscription tab → "Cancel subscription".
2. `POST /api/stripe/cancel` → `stripe.subscriptions.update(id, { cancel_at_period_end: true })`.
3. User stays PREMIUM until period end.
4. Stripe fires `customer.subscription.deleted` → `UserService.downgradeToFree(userId)`.

### Payment failure

- `invoice.payment_failed` → logged, user optionally emailed.
- After Stripe exhausts retries → `customer.subscription.deleted` → downgrade.

---

## Constraints

- Price ID is configured in `application.properties` (hardcoded) — see [[Known Issues]].
- No trial period.
- Account deletion ([[UC-06]]) does not cancel the Stripe subscription.
- No in-app refund — manual via Stripe dashboard.
