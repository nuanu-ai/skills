# Connection Setup

Install the host-native MagicPay plugin and authenticate the remote MagicPay
MCP that it declares. The plugin contains the skill and host metadata.
No MagicPay package, CLI, library, or local service is installed for the
browser flow.

Plugin activation starts one browser OAuth flow for both account verification
and MCP authorization. The same connection page collects the email and OTP,
grants MCP access, and redirects to the host. Do not ask the
user to send an email or OTP in chat, and do not run a separate MagicPay account
setup or login flow.

## Plugin lifecycle

Connecting, removing, and revoking MagicPay are host connection-management
actions. Use the host's plugin or connection settings to remove the connection
or revoke its authorization; there is no remote MagicPay removal or revocation
tool. Keep that lifecycle separate from payment orchestration and do not invent
an MCP command for it.

## Explain and connect

For a first-time setup request, explain the product before opening the
connection UI:

> MagicPay is the broader payment platform redesigned for AI agents. MagicCard
> is MagicPay's omnipayment tool: it has one unified balance, can be topped up
> through supported funding rails, and can pay through supported credit-card,
> crypto, and agentic payment methods. It gives me a secure way to prepare
> payments and request approvals while keeping sensitive payment details out of
> chat.

Then set the secure-input boundary:

> I’ll open a secure MagicPay window. Enter your email and OTP there—not in
> this chat. When it closes, I’ll verify the connection and continue.

Installing the plugin is not authentication. Before considering a catalog
refresh, inspect the exact installed `magicpay` connection and start its
host-native Connect action when it is logged out. The exact install, connect,
login, manual-handoff, and catalog-refresh actions for this host are listed in
[references/runtime-setup.md](references/runtime-setup.md), which every runtime
bundle provides for its own host; the canonical instructions never name a host.
The Connect action must open the same secure browser OAuth flow described
above. Never request or handle its email, OTP, authorization code, or tokens in
chat or shell arguments.

Do not tell the user that a fresh task or session will open OAuth. If the
host-native connection action cannot be initiated, give only the immediate
manual handoff named in the runtime setup reference and stop until it
completes.

Wait for that authorization yourself; never ask the user to confirm it in chat.
Once the connection UI is open, poll `get_magicpay_capabilities` on a bounded
interval until it answers or a reasonable deadline passes. The user finishing
email and OTP in the secure window is the completion signal, so a chat reply
such as "done" adds nothing and a user who simply closes the window would
otherwise strand the setup. Say only that you are waiting, and continue the
moment the capability call succeeds. Ask the user how it went solely when the
deadline passes, the host cannot poll, or the connection UI reports failure.

After OAuth completes, first probe the current task's callable catalog,
including any host-native deferred or lazy tool discovery, for
`get_magicpay_capabilities`. A tool omitted from the initial or eagerly shown
list is not evidence that it is unavailable. If the capability tool is
callable, stay in the current task, run the readiness sequence below, and
continue any request retained there.

Only when an actual current-task catalog lookup cannot discover or call a
required MagicPay tool should you say that the tool catalog is stale and that
this does not disprove the connection. Give the catalog-refresh action from the
runtime setup reference so the host can reuse the completed connection. Do not
repeat OAuth, install a MagicPay CLI, start a local MCP server, copy a token, or
claim that a refreshed task retained an unfinished request from the old task.

Call `get_magicpay_capabilities`. Continue only when it reports
`executionModes: ["client_browser"]`, `sessionAuthority: "remote_database"`, and
`browserPaymentRun.executionMode: "agent_direct"`. Treat its `setupState`, `nextAction`, and
`instructions` as the authoritative setup continuation. Authentication recovery
belongs to the host's MCP connection UI; the protected server cannot truthfully
return a pre-authentication account state before the host completes OAuth.

Before a fresh payment in a newly connected task, require the rail-specific
capability: `x402PaymentRun.status: "ready"` for x402,
`cryptoPaymentRun.status: "ready"` for crypto, or
`browserPaymentRun.status: "ready"` with `executionMode: "agent_direct"` before
browser payment. The
x402 and crypto workflow contract is `magicpay.payment-run/v1` schema `1.1`;
the browser contract is `magicpay.browser-payment/v1` schema `1.0`.
x402 requires minimum plugin version `0.2.0`.
Crypto requires minimum plugin version `0.2.0`.
Agent-direct browser payment requires `0.2.0`. Preserve the
selected rail's `selectedAgentId`. A blocked result is a pre-payment stop; do
not create a session or fall back to another browser-payment tool.

After capability discovery succeeds, call `get_magicpay_status` to verify the
authenticated agent identity and account health. Treat a bounded unavailable
status as an authentication or service-recovery stop; do not infer readiness
from capability discovery alone.

When capabilities and authenticated status are ready, silently call
`get_payment_balance` without an asset selector. Do not call
`show_payment_balance`, a retired card-balance tool, or any presentation tool
for this setup read. Use the response's exact atomic `available` value,
`presentation.scale`, and `presentation.assetId`; the tool's human-readable
content is produced by the shared money formatter. Never use floating point or
invent a currency.

For an explicit setup request, present exactly one of these completion
branches:

- **Funded balance.** If all three payment rails are ready, say:

  > **MagicPay is ready.**
  >
  > Your available balance is **{formatted balance}**.
  >
  > Your payment credentials stay protected and out of chat, and spending
  > stays subject to your MagicPay approval rules.
  >
  > Give it a try—send USDT or USDC, pay with x402, or ask me to buy something
  > online.

  If only some rails are ready, keep the first three paragraphs unchanged and
  offer only the actions whose rail-specific capabilities report `ready`.
- **Exact zero balance.** Keep the same heading, exact formatted balance, and
  credential/approval reassurance, then say: "Add funds to start using
  MagicPay, or ask me to top up your MagicCard." Do not create a top-up link or
  open a widget unless the user asks or the authoritative workflow returns
  `funding_required`.
- **Unavailable or malformed balance.** Say: "**MagicPay is ready.** I couldn't
  read your available balance right now. Your payment credentials stay
  protected and out of chat, and spending stays subject to your MagicPay
  approval rules." Do not infer zero, manufacture an amount or currency, or
  claim that the balance call succeeded.

Then continue the user's original request without asking them to repeat it only
when the host retained that request in this same task. In a catalog-refresh
fallback, act only on the request available after refresh without claiming that
prior task context carried over. In later already-connected tasks, do not repeat
setup onboarding; verify only the capabilities required by the requested action.
