# Status Meanings

Interpret a status within its returned request, run, session, or operation;
the same word is not a universal workflow transition.

## Requests and Memory

- Request `waiting_user`: preserve the exact request and follow
  [requests.md](requests.md). A generic choice also follows
  [choices.md](choices.md); payment approval is not a chat choice.
- Request `fulfilled`: consume only the stored artifact and exact continuation.
  For a choice, report the recorded selected ID, never a local draft.
  For protected Memory, resume the original materializer/resolver; generic
  request reads, waits, and claims never release the collected values.
- Request `denied`, `expired`, `canceled`, or `failed`: stop that request; do
  not fabricate a selection or present its options as a new pending prompt.
- Memory `ready`: use only the returned values for the approved current scope.
- Memory `request_required`: resolve the exact returned request, then rerun
  the same materialization or resolver input.
- Memory `fallback_required`: follow its safe fallback, without partial fill.

## Browser and payment runs

- `clicked`: dispatch occurred; settlement remains unknown.
- `click_uncertain`: dispatch may have occurred; reconcile and never replay.
- "merchant_confirmed": the merchant visibly confirmed the submitted checkout.
  Close the workflow with structured `checkoutOutcome`; provider settlement may
  remain pending on the exact operation.
- `awaiting_approval`: retain stable operation-owned `approval.requestId`, then
  continue the same remote session using the distinct routable UUID
  `approval.runtimeRequestId`; do not create another approval.
- Payment-run `running`: carry the same `runId` and `nextProgressCursor` into
  `wait_payment`; it is neither failure nor permission to restart.
- Payment-run `waiting_for_user`: report the exact returned request URL and,
  immediately call `wait_payment` on the same run; its one bounded call polls
  every three seconds while approval is open. Never require a chat reply or
  create a replacement request.
- Payment-run `reconciliation_required`: reconcile only its exact returned
  operation. A missing x402 result after financial completion does not permit
  another purchase.
- funding_required: only authoritative insufficient unified user balance
  automatically opens `show_topup`. Durable top-up settlement may wake only the
  same approved, unchanged, definitely-not-submitted operation; continue its
  same run and never replace it.
- service_unavailable with card-pool insufficiency: this is a temporary card
  service capacity issue, not a user-balance funding request. Do not call any
  top-up or fresh payment tool; direct the user to support@magiccard.ai.
- `revoked`: canceled pre-submit approval authority. The historical approval
  decision may remain auditable, but it cannot resume, materialize, or authorize
  any payment operation.
- `external_not_submitted`: follow only the exact operation's returned retry
  guidance; provider non-submission is not permission to create a replacement.
- `external_pending`: a direct transfer was submitted successfully and is in
  normal blockchain confirmation. Report that no action is needed and MagicPay
  will notify the user after settlement, stop foreground polling, and retain the
  exact operation/session. Only `completed` proves settlement.
- `reconciliation_required`: reconcile the same operation only.
- `completed`: terminal only when the operation/provider evidence agrees.
- `definitively_failed`: terminal for that operation attempt. Do not retry when
  `retry.allowed:false` or the failure is non-retryable. Failure alone does not
  prove release; follow the terminal closure rules below.
- `canceled`: terminal for workflow authority immediately. A separately
  preserved dispatched or uncertain operation may still be nonterminal and
  require same-operation reconciliation; cancellation does not release its
  held Ledger reservation by itself. Trust only returned `cleanupDisposition`
  and `freshStartAllowed` for cleanup and fresh-attempt policy.
- `cleanup_pending`: the workflow is canceled but the exact operation release is
  not verified. Replay only the same cancellation as directed by
  `retry_cleanup_same_operation`.
- `failed`: the workflow closes through `fail_checkout_session` with one exact
  failure code and stable idempotency key. A separately unresolved or possibly
  dispatched operation remains bound to the returned same-operation
  reconciliation action; failure alone does not release it or authorize a new
  payment. Only the complete safe fresh-start disposition below plus a later
  explicit user request creates new authority, with all-new identities.

## Terminal release and a later payment

A later, separately user-authorized payment may start only after the owning
workflow is durably closed and its current result explicitly returns all of:

- `cleanupDisposition`: `released_pre_submit` or `released_after_failure`;
- `settlementStatus`: `failed` or `not_started`;
- `freshStartAllowed: true`; and
- `nextAction: none`.

`released_pre_submit` proves the exact non-submitted operation authority or
hold was released. `released_after_failure` proves the exact submitted native
operation is definitively failed and its own Ledger release consequence was
recorded. Neither disposition makes the old attempt retryable or releases
unrelated reservations. Missing or unresolved evidence forbids a fresh start.
Use new workflow, request, approval, operation, reservation, run and idempotency
identities; never reuse old authority.

Status, cancellation, and reconciliation reads remain silent supporting calls.
An error, hard stop, separately pending reconciliation, or required user action does not
request a widget; report it in normal conversation unless the user separately
asks for the corresponding view. When a `show_*` tool says no view opens on
this host, present its returned `hosted_url` or `app_url` in normal
conversation; when a run or request returns `request_link_reason` instead of
`request_url`, say that the approval link is unavailable and that the user can
decide in the MagicPay app or Telegram, keeping the same run and request. For
automatic funding and new-choice presentation, follow [commands.md](commands.md).
