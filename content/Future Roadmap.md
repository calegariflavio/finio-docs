# Finio — Future Roadmap

*Last updated: 2026-07-25*

These are features that were consciously deferred from the initial build. They are not forgotten — they are documented here so the "why not yet" is explicit.

---

## Near-term (post-launch, v1.1)

### Fix SMTP reliability
- Replace Gmail SMTP with a proper transactional email provider (Resend, SendGrid, Postmark).
- Gmail app passwords expire and hit rate limits. Not appropriate for production traffic.

### Bank feed integration (read-only)
- Plaid (US/Canada) or Open Banking API for automatic transaction import.
- Would replace/complement manual CSV import.
- Significant complexity: OAuth flow, data sync, category auto-mapping.

### Recurring transactions for FREE users
- Currently PREMIUM-only. Might be worth allowing 1-2 recurring templates on FREE to drive stickiness.

### Budget rollover
- Carry unspent budget from one month to the next.
- Requires a "rollover amount" field on Budget and a scheduled job on month-end.

### Transaction split
- Split one transaction across multiple categories.
- E.g., a $100 grocery trip = $60 Food + $40 Household.

---

## Medium-term (v1.2 — v2.0)

### PDF report export
- Monthly report as a downloadable PDF (income, expenses, budget summary, charts).
- Could use a headless browser (Puppeteer) or a Java PDF library (iText).

### Push notifications (web)
- Browser push via the Push API / Service Worker.
- Useful for: budget alert when you're over threshold, recurring transaction due, monthly report ready.

### Mobile app (React Native)
- The backend API is already mobile-ready. The frontend would need to be ported.
- Could use Expo for a managed workflow.

### Multi-currency support
- Currently currency is a display preference only — all amounts stored as-is.
- Full multi-currency would require: storing original currency + exchange rate + converted amount, and a daily rates feed.

### Shared accounts / household mode
- Allow two users to share a transaction pool.
- Complex: auth, permissions, conflict resolution on edits.

### Two-factor authentication (TOTP)
- Time-based OTP (Google Authenticator) for login security.
- The OTP infrastructure already exists for password changes — login TOTP is the natural extension.

### Transaction receipt attachments
- Photo upload per transaction for receipts.
- Needs blob storage (S3-compatible) on the backend.

---

## Long-term / exploratory

### AI-powered category suggestions
- Auto-categorize transactions from description text using a small LLM.
- Would pair well with bank feed integration.

### Financial health score
- Composite metric: savings rate, budget adherence, debt-to-asset ratio.
- Requires enough transaction history to be meaningful.

### Savings recommendations
- "You spend $X/month on dining out — here's what you could save."
- Needs real usage data (500+ transactions) to tune.

### Social / accountability features
- Share a savings goal publicly (link).
- Anonymous spending comparisons ("you spend 20% more than similar users on groceries").

### Admin: user impersonation
- Admin views a user's dashboard as that user.
- Useful for support. Requires careful audit logging.

### Admin: refund flow
- Currently refunds are done manually via the Stripe dashboard.
- An in-app refund button would trigger `stripe.refunds.create()` and downgrade the user's plan.

---

## Permanently deferred

| Item | Reason |
|---|---|
| Cryptocurrency portfolio tracking | Out of scope for a personal finance app. Different data model. |
| Investment portfolio / stock tracking | Same — requires market data feeds, different domain. |
| Tax filing assistance | Requires jurisdiction-specific tax rules. Too complex for a portfolio project. |
| Multiple accounts per user (bank accounts, credit cards) | The current model treats the user as a single ledger. Multi-account would require rethinking the transaction schema. |
