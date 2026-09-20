# Subscriptions

Inspect the final visible offer before classifying a Stripe link. Stripe Payment
Links and Checkout can sell recurring plans or one-time purchases; the hostname
and a save-card checkbox do not establish a subscription. Look for the billing
period, renewal language and selected plan/quantity. Paying an existing invoice
is payment of that invoice, not a new subscription signup.

Create a checkout with `type: "subscription"`. Recurring intent follows the
session. On `run_browser_payment`, use `paymentType: "subscription"` and the
actual final action, including `subscribe`. Payment and recurring terms appear
in one MagicPay approval; continue the same run after approval or funding.

Provide optional `subscriptionDetails` at checkout creation or on the browser
payment run: `recurringAmount`, `recurringCurrency`, `intervalUnit`, `intervalCount`,
`recurringAmountKind`, optional legacy `interval`, `trialEndAt`, `nextRenewalAt`,
`expiresAt`, `autoRenews`, `cancellationUrl`, `cancellationHint`, `serviceLogoUrl`,
and `estimatedRenewalUsd`. Decide reasonable dates from the checkout and your own
judgment. Missing values are fine; no invoice, proof upload or separate date
verification is needed. For non-USD renewals, the USD estimate should describe
the future recurring charge, which can differ from today's trial/discount.

Set recurring money to the actual full charge per billing period, not the amount
due today and not a manually divided monthly number. A $240 annual plan uses
`recurringAmount: 240`, `intervalUnit: "year"`, `intervalCount: 1`; $60 every three
months uses `intervalUnit: "month"`, `intervalCount: 3`. MagicPay stores a monthly
equivalent separately. Preserve the observed currency, quantity, tax qualifiers,
introductory price and setup fee. Use `recurringAmountKind: "variable"` for
usage-based/mixed pricing, or `"unknown"` when the recurring price is unavailable;
do not substitute zero or a first-charge discount. Reinspect changed plan or
quantity before submission; changed money, interval count or trial end requires
updated approval terms on the existing checkout. Never create a second purchase
to get past a conflict.

For Stripe-hosted checkout, keep `merchantDomain` equal to the checkout host and
supply the separately observed `sellerDomain` and `sellerName` on
`run_browser_payment` when known. Do not use Stripe as the seller or invent an
order reference from a URL. Missing seller identity may require explicit
attachment to the exact operation rather than automatic email matching.

Logos and unsubscribe hints are optional. Use a public HTTPS logo observed on the
actual service, without credentials, signed links or a separate search detour.
If unavailable, omit it and continue; MagicPay shows initials. Provide a short
plain-text `cancellationHint`, for example “Settings > Billing > Manage plan >
Cancel subscription; choose cancel at period end to keep paid access.” If it is
unknown, leave it absent; the UI supplies a general guide. Never invent a portal
URL. A missing logo, hint or invoice must not delay the payment reply.

Zero-due trials are not supported by the current positive-charge browser run.
Report this limitation before any enrollment submission; never invent a nominal
charge, weaken the amount, or use another payment route to bypass it. A paid
signup does not prove that a retained card or future merchant renewal works.

After signup, report updated details with `record_browser_payment_result`.
Dates and hints are editable metadata; commercial amounts and recurrence still
belong to the approved payment. Save any original invoice to the exact operation/session using
[invoices.md](invoices.md), without waiting for extraction or replacing the
browser amount with a missing AI value. The saved subscription links to that same
original or hosted invoice; say it is attached only after the returned evidence
confirms it. The first invoice does not stand for later renewal invoices.

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
