# MCP Routing Map

MCP discovery is authoritative for exact arguments and results. This reference
only distinguishes starting routes, continuations, and explicit presentation.
Use opaque IDs exactly as returned.

## Starting routes

| User intent | Start here |
| --- | --- |
| Connect or recover | `get_magicpay_capabilities`, then `get_magicpay_status` |
| Account readiness | `account_status` |
| Identity-verification help | `help_identity_verification` |
| Recent user activity | `list_recent_transactions` |
| Balance | `get_payment_balance` |
| Generic top-up | `show_topup` |
| Funding methods, link, or addresses | Use the exact funding action requested |
| New crypto transfer | `run_crypto_transfer` |
| Known raw x402 resource | `run_x402_payment` with the exact `httpRequest` envelope |
| Known checkout destination | `create_checkout_session` |
| Unknown product or provider method | `search_provider_methods` |
| Seller trust before paying | `check_merchant` with up to ten documented seller requests |
| Optional choice without an existing session | `begin_request_session`, then `request_choice` |
| Existing payment status | `get_payment_operation` |
| Invoice or original-document status | `get_payment_operation` for the exact operation; see [invoices.md](invoices.md) |
| Attach a PDF original or supported HTML receipt link | `attach_payment_invoice` with the exact operation and supported host file input; see [invoices.md](invoices.md) |
| Read agent correspondence | `list_agent_email_threads`, `read_agent_email_thread` for the exact owned agent |
| Prepare and send agent email | `prepare_agent_email`, normal `wait_request` / supported decision flow, then `send_agent_email` when directed; see [email.md](email.md) |
| Recover an uncertain email send | `get_agent_email_send` with the same send and agent IDs |
| Manage saved Memory | Use the direct value-free CRUD action matching the intent |
| Use Memory in an active session | `get_memory_footprint`, then exact `materialize_memory_items` or v3 `resolve_browser_form_values` |
| Existing request, session, or operation | For payment execution, use only its returned `nextAction`; authorized read-only diagnosis remains available |

A request that already names a merchant, recipient, or site does not require
provider discovery: find its official destination in the host browser, then
create the checkout session.

## Silent work and presentation

Operational reads, mutations, approval, waits, status checks, and reconciliation
stay silent inside a broader task. Open a widget only for the current user's
explicit request to see that exact surface:

- `show_account_status`
- `show_memory_items`
- `show_payment_balance`
- `show_payment_operation`
- `show_topup`
- `show_session`
- `show_session_request`
- `show_sessions`
- `show_subscriptions`

Automatic presentation cases:

- Authoritative unified balance returns `funding_required`: call `show_topup`
  once. Service, policy, approval, card-pool, and ambiguous-submission errors
  are not funding requests.
- A new `request_choice` returns `waiting_user`: show its widget or returned
  `request_url` and one chat presentation, either unchanged `chatMessage` or a
  faithful host-native picker. Do not invoke another view tool or repeat the
  prompt for a terminal replay. See [choices.md](choices.md).
- A `prepare_agent_email` result needs approval: show its existing full-draft
  request widget or `request_url` before a decision. Keep the same send and
  request; see [email.md](email.md).

## Exact continuations

- Continue a composed payment with `wait_payment` using the same `runId` and
  progress cursor.
- Read one operation with `get_payment_operation`; call
  `reconcile_payment_operation` only for that exact operation and only when its
  state directs reconciliation.
- Continue a runtime request with `get_request`, `wait_request`,
  `decide_request`, or `confirm_request_otp` using its exact request identity.
- Continue a generic choice with `decide_request` using `confirmed` and its
  exact opaque `selectedChoiceId`, then `wait_request` on the same IDs.
- Continue a checkout with its exact session lifecycle tools. Cancellation or
  failure closure never proves settlement.
- MagicSearch has no continuation state. Read a relevant provider-method
  result, verify current documentation, and execute with an available agent
  capability. If that work is paid, begin a separate exact MagicPay operation;
  never take a debit ceiling or payment authority from registry prose.
- Continue a Memory choice with `decide_request` using `choose_candidate` and
  its exact `selectedChoiceId`. Continue approval or collection through the
  exact returned request, then re-run the same materialization/resolver call.

## Internal tools

Widget-internal tools are app-only in MCP visibility, not model routes.
Payment instruments are internal infrastructure: inventory, provider-card
details, diagnostic balances, and provider-card history remain unavailable.
Route every user balance request through the unified payment-balance tools.
