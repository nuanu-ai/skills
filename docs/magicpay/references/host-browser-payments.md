# Direct Browser Payments

Use this path when `browserPaymentRun.status: "ready"` and
`executionMode: "agent_direct"`. The host's built-in Browser owns the live tab.
MagicPay owns the exact approval, operation, payment-scoped card, result, and
reconciliation state. Do not add a second browser controller.
If the host cannot inspect and fill the required live targets, report the
unsupported capability. Shared guidance is not proof of another host's live
browser support.

## One run, one approval

Create the checkout session for the known destination. Use the Browser to reach
the actual payment-dispatch surface without activating it, then observe the
merchant/recipient, exact merchant amount and currency, recurrence, payment
type, actual final-action meaning, and the ordinary semantic roles visible now.

For recurring signup, use [subscriptions.md](subscriptions.md) to supply the
recurring terms and optional dates/hints with this same checkout.

## Fixed USD price for non-USD checkouts

For a USD checkout, pass the exact merchant money unchanged as both `amount`
and `maximumDebit`. For another merchant currency, preserve that exact local
money in `amount` and form one fixed USD `maximumDebit` before the first
`run_browser_payment` call:

1. Fetch only the direct Frankfurter pair
   `https://api.frankfurter.dev/v2/rate/{MERCHANT_CURRENCY}/USD`, replacing the
   placeholder with the observed uppercase ISO currency. Require a successful
   JSON response whose `base` is that merchant currency, whose `quote` is
   `USD`, and whose `rate` is positive. Do not use a search result, a broad rate
   table, an inverted pair, or a model-generated rate.
2. Treat the returned rate as USD per one merchant-currency unit. Apply the
   fixed 5% beta FX allowance and round upward exactly once to a USD cent:
   `usdCents = ceil(merchantMajorAmount × rate × 1.05 × 100)`.
3. Set `maximumDebit` to `{ quantity: String(usdCents), scale: 2, currency:
   "USD" }`. Never relabel the local amount as USD or compare the two atomic
   quantities.
4. Show the user the exact merchant money, the Frankfurter pair/date/rate, the
   fixed 5% allowance, and the resulting fixed USD price. If the direct fetch,
   pair validation, or calculation fails, stop before creating a payment run.

The first run call binds that fixed USD price. Reuse the same `maximumDebit`,
`clientRequestId`, and `runId` throughout approval, waits, ordinary-field
resolution, browser preparation, and exact-operation recovery. Never refetch
or reprice the same run. A decline, possible submission, uncertain click, or
reconciliation requirement never permits a replacement run or another final
click. A fresh run may use a fresh rate only when the prior exact checkout has
durably returned fresh-start authority under the normal recovery rules.

Call `run_browser_payment` once with one stable `clientRequestId`. Do not wrap it
in separate balance, capability, approval, or card-preparation calls. Approval
creates checkout authority for the unchanged merchant and payment. Honor the
returned expiry and host-required confirmations; MagicPay adds no redundant
confirmation of the same approved facts, and neither do you. After MagicPay
approval the only confirmation left is one the host itself requires for the
final action; do not ask the user to approve the same payment again in chat.
If email, name, phone, country, billing address, city, region, or postal code
appears later, add it to the sorted unique role union and replay the same run.
For receipt email precedence and an inbox that is not ready yet, follow
[invoices.md](invoices.md); inbox provisioning must not delay this run.
Never remove a previously observed role. Passwords, OTPs, identity documents,
tax identifiers, bank credentials, private keys, seeds, and unrelated secrets
are outside this payment authority. A separately authorized non-payment Memory
task follows [memory.md](memory.md); otherwise hand off safely.

| Run state | Continue with |
| --- | --- |
| `waiting_for_user` | Present the exact approval URL; wait on the same `runId`. |
| `ordinary_field_required` | Resolve only the returned roles and resume the same run; this is not a new payment approval. |
| `preparing_browser` | Wait on the same run. |
| `ready_for_browser` | Fill the unchanged checkout in the exact live tab. |
| `external_pending` | Reconcile the same operation; never click again. |
| `reconciliation_required` | Reconcile the exact operation; never replace it. |
| `completed` | Report success only from the durable completed operation. |

## Browser ownership and rendered state

Use the host's normal visual and interaction capabilities for the unchanged
approved checkout. Inspect the intended visible fields and final control; a
hidden DOM entry or accessibility presence alone does not prove visibility.
Use screenshots when they help resolve ambiguity, without a prescribed count
or field order. After a page transition, discard stale targets and reacquire
current controls. Stop if the bound payment facts changed.

Payment-method selectors, accordions, and next/continue controls may reveal a
form without submitting payment. Determine the actual action from the rendered
page, not its label. Do not record these intermediate interactions as payment
dispatch. Correct merchant validation in the same run; refill only allowed
fields that current evidence shows need correction, without unbounded loops.

Fill returned values using host-supported browser input for the exact approved
tab, including documented host REPL calls where applicable. A clipboard or focus
error from `.fill()` does not require repeating that same method. Re-observe the
intended visible, enabled field, reacquire its current frame and control when
needed, and use supported targeted native per-key input. Click the intended
control only if needed. This applies to ordinary fields and authorized payment
fields, including iframe fields. Replace partial input only when current
evidence shows it needs correction. Verify ordinary field values normally;
for payment credentials, verify presence and merchant validation without
reading values back.
Do not invent a generic sensitive-fill API, inject credentials with arbitrary
page-evaluation code, or switch to raw browser-protocol control.
Keep final dispatch separate from fill and navigation: invoke the identified
payment control once in an isolated host action.

Native screenshots may incidentally contain payment fields before or after
fill. Keep them within host reasoning; never export them as evidence or extract
card values from them. The V1 visibility boundary is defined in the skill router;
neither a screenshot nor a successful fill authorizes submission or proves
settlement.

## Pre-dispatch Browser interruption

A tab binding can become stale without invalidating the Browser binding. When a
fill/navigation call is definitely before the isolated final action, do not
call `record_browser_payment_result`, fail, cancel, release, or create another
run merely because of the interruption. Recover within the host's documented
capabilities. If only a field action failed, keep the live page and use the
targeted input recovery above. Do not use `goto(currentUrl)` as a reload or
input-recovery step: a checkout can depend on POST state that a new GET loses.
If the tab binding is stale:

1. discard only the stale tab binding;
2. reacquire the exact live tab from the existing Browser binding, or reopen
   the same approved HTTPS checkout when that tab no longer exists;
3. replay the same `clientRequestId` and `runId`;
4. re-observe and refill only missing allowed fields; and
5. continue with the same execution-attempt ID.

If bounded recovery cannot restore the unchanged pre-dispatch checkout, hand
the exact checkout to the user without replacing the payment. Do not infer that
a tab or browser can be reopened after possible dispatch.

If the interruption overlaps the isolated final-action call, record
`click_uncertain` and reconcile the same operation. Never reopen for another
click. A timeout or missing output is not permission to retry dispatch.

## Challenges and unusual states

Handle challenges under the host's capabilities and policy; hand the same tab
to the user when needed. A pre-dispatch challenge does not fail or replace the
payment. After any possible dispatch, observation is read-only and recovery
stays with the same operation.

## Record the result once

After the final action, make a bounded fresh observation and call
`record_browser_payment_result` for the exact run and execution attempt.
Observation failure is not permission to click again:

- `not_clicked` only when the isolated payment-dispatch control definitely was
  not activated;
- `clicked` when it was activated, with the separately observed submission and
  merchant outcome; or
- `click_uncertain` when activation may have occurred.

Pass an ordinary receipt address only in `checkoutEmail`, never in
`valueFreeEvidence`. Approval, card materialization, field fill, click, and
merchant visibility are not settlement. When the composed result is
`completed`, stop; do not call a second checkout closer.
Handle returned `invoiceFollowUp` and later original-document processing through
[invoices.md](invoices.md), without waiting for mail or AI before the payment reply.
