# Remote Workflow

Choose the focused contract; this map does not add a second protocol.

| Intent | Start and continuation |
| --- | --- |
| Already-known ordinary browser fields | Host browser directly, within the user's existing task authority. |
| Browser form needing Memory | `begin_browser_form` → footprint → exact resolver → returned request if needed → same resolver → whole-batch fill. See [memory.md](memory.md). |
| Known checkout URL | `create_checkout_session` → host inspects actual checkout → `run_browser_payment` → same run → host fill and one authorized final action → bounded observation → `record_browser_payment_result`. See [host-browser-payments.md](host-browser-payments.md). |
| Exact crypto or x402 request | Corresponding composed payment run → `wait_payment` on the same run → settlement/recovery. See [payment-operations.md](payment-operations.md). |
| Unknown provider | `search_provider_methods` → verify current official docs → exact request under user authority; discovery grants no payment authority. |
| Candidate sellers | `check_merchant` for every candidate → skip `avoid`, surface `caution`, continue on `proceed` or `unknown`; verdicts are advisory. |
| Material shortlist | One normalized choice request → exact durable selection → selected branch only. See [choices.md](choices.md). |

The host owns browsing. MagicPay owns authorization, materialization, and durable
payment state. A Memory fill is not a new submit authority; an intermediate
checkout control is not necessarily payment dispatch. After possible dispatch,
do not click again.

Preserve every exact session, request, run, attempt, operation, revision,
idempotency key, and returned continuation. Use [requests.md](requests.md) for
request waiting and hosted collection; abort returns control rather than
forcing a new wait loop. Host-mandated confirmations still apply.

Report settlement only from the durable completed operation. When the composed
browser result is fully projected and `completed`, its workflow is already
terminal: do not call a second closer. Use `complete_checkout_session` only
when its exact current continuation directs it.

Cancellation closes workflow authority, not necessarily financial exposure.
Follow the exact cleanup and same-operation reconciliation result. Preserve the
owning workflow's status: an already canceled workflow uses cancellation
cleanup; an open or failed workflow with non-retryable failure uses
`fail_checkout_session`. Never relabel a completed or canceled workflow.
Neither failure nor expiry permits a replacement payment. [statuses.md](statuses.md)
owns the complete recovery and fresh-start rules.
