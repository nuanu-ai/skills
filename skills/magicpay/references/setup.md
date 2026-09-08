# Connection Setup

Install the host-native MagicPay plugin and authenticate the remote MagicPay
MCP that it declares. The plugin contains the skill and host metadata.
No MagicPay package, CLI, library, or local service is installed for the
browser flow.

Any previously installed MagicPay CLI is an abandoned path. Ignore it for
setup, login, verification, and recovery; its presence does not establish a
connection. Do not execute or upgrade it. Use the native plugin and remote MCP.

When authentication is missing, start one browser OAuth flow for both account
verification and MCP authorization. The same connection page collects the email
and OTP, grants MCP access, and redirects to the host. Do not ask the
user to send an email or OTP in chat, and do not run a separate MagicPay account
setup or login flow.

## Plugin lifecycle

Connecting, removing, and revoking MagicPay are host connection-management
actions. Use the host's plugin or connection settings to remove the connection
or revoke its authorization; there is no remote MagicPay removal or revocation
tool. Keep that lifecycle separate from payment orchestration and do not invent
an MCP command for it.

Inspect the installed plugin and its bundled connection first. Keep a correct
installation and existing authorization; a repeated setup request is not a
request to update, reinstall, or sign in again. Keep a supported installed
version even when the marketplace advertises a newer release.
Missing tools or an unavailable plugin inventory leave installation and auth
state unknown; they do not mean the plugin is absent. If inventory cannot be
read, use the runtime's plugin-details handoff to establish that state. For an
installed plugin, continue through Connect when sign-in is needed; choose
Install/Add only when absence is confirmed. If already connected, discover its
tools and verify readiness. Do not send an installed plugin back through Install.
Use an actually callable, eligible native installation action, or the
host's supported commands when permitted, as listed in the runtime setup
reference. The agent performs those commands. Do not
automate the host's own settings UI or bypass a denied host action. During an
actual channel change, log out the old exact connection before removing its
plugin; preserve unrelated integrations and all remote account/payment state.

Installation, activation in the current conversation, OAuth, and readiness are
separate. Follow the host's activation result: a cold install may need activation
before its Connect action exists. After authorization has completed, try
current-conversation discovery before requesting a reload. A user-entered reload
or connector attachment is same-conversation recovery, not automatic setup.

## Explain and connect

For a first-time setup request, explain the product before opening the
connection UI:

> MagicPay is the broader payment platform redesigned for AI agents. MagicCard
> is MagicPay's omnipayment tool: it has one unified balance, can be topped up
> through supported funding rails, and can pay through supported credit-card,
> crypto, and agentic payment methods. It gives me a secure way to prepare
> payments and request approvals while keeping sensitive payment details out of
> chat.

When you can initiate Connect, set the secure-input boundary:

> I’ll open a secure MagicPay window. Enter your email and OTP there—not in
> this chat. After the host confirms authorization, I’ll verify the connection
> and continue.

If no connection action is callable, name the runtime's native Connect button
instead of promising to open the window. Missing host-management tools do not
establish a server outage or an authentication failure.

Installing the plugin is not authentication. Inspect the exact installed
`magicpay` connection: check readiness when already authorized, and start Connect
only when authentication is missing. Activate a cold installation first when
the host requires it. The install, activation, connect, and recovery actions for
this host are listed in
[references/runtime-setup.md](references/runtime-setup.md), which every runtime
bundle provides for its own host; the canonical instructions never name a host.
The Connect action must open the same secure browser OAuth flow described
above. Never request or handle its email, OTP, authorization code, or tokens in
chat or shell arguments.

Do not tell the user that a fresh task or session will open OAuth. If neither a
supported native action nor its permitted command can initiate the connection,
report the blocked phase and give only the immediate manual handoff named in
the runtime setup reference. A manual fallback is not completed single-prompt
setup. A canceled or denied action stops this attempt; do not retry through
another route.

Wait for host-reported authorization completion yourself when supported; do not
require a "done" reply. Use a bounded wait or discovery interval and continue
when it succeeds. An opened or closed browser window is not proof of OAuth
completion. Report a cancellation, failure, or deadline at the phase it occurred,
without reopening sign-in automatically.

After OAuth completes, first probe the current task's callable catalog,
including any host-native deferred or lazy tool discovery, for
`get_magicpay_capabilities`. A tool omitted from the initial or eagerly shown
list is not evidence that it is unavailable. If the capability tool is
callable, stay in the current task, run the readiness sequence below, and
continue any request retained there.

If an actual current-task catalog lookup cannot discover or call a required
MagicPay tool, follow the runtime setup reference's bounded supported refresh.
Use a native reload only when it is actually exposed to the model; otherwise
give the documented same-conversation user action when available, or report the
observed host limit. Missing tools alone do
not diagnose stale state, failed authentication, or a particular host limitation.
Keep installation, host-reported authorization, tool availability, and service
readiness separate: report only the phases supported by evidence. Do not repeat
OAuth, install a MagicPay CLI, start a local MCP server, copy a token, or claim
that a refreshed task retained an unfinished request from the old task.

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
Use each rail's advertised `minimumPluginVersion` as its compatibility floor;
compare the installed base semantic version without a prerelease suffix. Do not
replace that floor with the newest published version. Verify the exact endpoint
and environment for the selected channel. Guide, plugin, and MCP revisions are
diagnostics; independently deployed services do not need matching Git SHAs.
Preserve the selected rail's `selectedAgentId`. A blocked result is a pre-payment
stop; do not create a session or fall back to another browser-payment tool.

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
