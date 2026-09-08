# Subscriptions

Create a checkout with `type: "subscription"`. Recurring intent follows the
session. On `run_browser_payment`, use `paymentType: "subscription"` and the
actual final action, including `subscribe`. Payment and recurring terms appear
in one MagicPay approval; continue the same run after approval or funding.

Provide optional `subscriptionDetails` at checkout creation or on the browser
payment run: `recurringAmount`, `recurringCurrency`, `interval`, `nextRenewalAt`,
`expiresAt`, `autoRenews`, `cancellationUrl`, `cancellationHint`, and
`estimatedRenewalUsd`. Decide reasonable dates from the checkout and your own
judgment. Missing values are fine; no invoice, proof upload or separate date
verification is needed. For non-USD renewals, the USD estimate should describe
the future recurring charge, which can differ from today's trial/discount.

After signup, report updated details with `record_browser_payment_result`.
Dates and hints are editable metadata; commercial amounts and recurrence still
belong to the approved payment. Save any original invoice to the session using
[invoices.md](invoices.md), without waiting for extraction or replacing the
browser amount with a missing AI value.

Use `list_subscriptions` or `show_subscriptions` to find saved subscriptions,
renewal/expiry dates and cancellation progress. An expected renewal date is a
reminder schedule, not proof that a merchant charge settled.

When the user asks to cancel:

1. Call `cancel_subscription({ subscriptionId })` for current details and hints.
2. Use the host browser to unsubscribe at the merchant. Hints are guidance;
   adapt to the live page. Prefer stopping renewal while keeping paid access.
3. Call the same tool with `outcome: "cancelled"` or `"already_cancelled"`, an
   expiration date if known, and a brief note. Your observation is sufficient;
   no additional backend evidence verifier is needed. If login or another step
   needs the user, report `"needs_user"`; otherwise report `"unsuccessful"` and
   the reason. Neither outcome turns renewal off.

The UI Cancel button opens a modal with a prompt to copy into an agent. Opening
or copying it does not cancel the merchant subscription. Cancellation does not
create a payment or a new signup. Future reminders stop after reported success;
paid access remains visible until the saved expiration date.

Reminders use the saved renewal schedule and existing notification preferences.
For a known USD charge or USD estimate they include the current balance
shortfall. Unknown prices/balances still allow a reminder without inventing a
minimum top-up. The cron does not charge, cancel or top up anything.
