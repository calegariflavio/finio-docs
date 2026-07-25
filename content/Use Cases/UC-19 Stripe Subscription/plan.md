# UC-19 — Implementation Plan

## Frontend

| File | Role |
|---|---|
| `pages/settings/SubscriptionTab.tsx` | Plan status card, upgrade/cancel buttons |
| `components/PremiumGate.tsx` | Feature-level upgrade CTAs throughout the app |

After checkout redirect, the success/cancel return URLs send the user back to the settings subscription tab. The JWT still reflects the old plan — user must refresh or the app calls `/api/auth/me` again to pick up the new plan.

## Backend

| Layer | File |
|---|---|
| Controller | `StripeController` |
| Service | `StripeService` |
| Webhook handler | Embedded in `StripeService.handleWebhookEvent()` |

The webhook endpoint (`POST /api/stripe/webhook`) is in the public endpoints list (no JWT required). Stripe signature is verified via `Stripe.constructEvent(payload, sig, webhookSecret)`.

Events handled:
- `checkout.session.completed` → `upgradeToPremium()`
- `customer.subscription.deleted` → `downgradeToFree()`
- `invoice.payment_failed` → log (no immediate downgrade)

## API

```
POST /api/stripe/checkout   200: { data: { checkoutUrl } }
POST /api/stripe/cancel     200: success
POST /api/stripe/webhook    200: acknowledged (Stripe-Signature required)
```
