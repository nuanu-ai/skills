# Runtime Setup: MagicPay host plugin

This plugin serves Codex, Claude Code, OpenClaw, Grok Build, and Grok Bot. Use
only the section for the host you are running in; the canonical instructions
in [setup.md](setup.md) never name a host.

## Runtime Setup: Codex

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Codex commands; the canonical instructions stay host-neutral.

### Install

- `codex plugin list` first: exactly one installed `magicpay@…` selector may
  remain. Remove any other before installing.
- Prefer the user's own plugin manager, **Plugins → MagicPay → Install**. Only
  an install made inside the running app rebuilds that app's plugin runtime;
  `codex plugin add` runs in a separate process and reaches only sessions
  started after it.
- Development channel, from a `codex` terminal session:
  `codex plugin marketplace add nuanu-ai/skills --ref staging` registers
  marketplace `nuanu-skills-staging`, then
  `codex plugin add magicpay@nuanu-skills-staging`.
- Production channel, after promotion: the `nuanu-ai/skills` marketplace at its
  stable ref and selector `magicpay@nuanu-skills`.

### Connect

- Use the plugin's Connect action. If no model-visible Connect action is
  available but the bundled `magicpay` MCP registration exists, run the host's
  built-in `codex mcp login magicpay`. This is Codex connection management, not
  a MagicPay CLI or a second account login.
- Manual handoff when no Connect action can be initiated:
  **Plugins → MagicPay → Connect**.

### Catalog refresh

- After OAuth, first use Codex's current-task deferred tool discovery to find
  and call `get_magicpay_capabilities`. Continue setup and any retained request
  in this task when the call succeeds. A tool omitted from the initial visible
  list is not evidence that it is unavailable, and a successful MagicPay call
  must not be followed by a **New task** instruction.
- Only when an actual current-task lookup cannot discover or call a required
  MagicPay tool should you tell the user to click **New task** in the Codex
  sidebar. The new task reuses completed OAuth; it is a catalog fallback, not
  the action that opens OAuth.
- When a new task still cannot discover the tools, this app never loaded the
  plugin at all: the install happened in another process. Ask the user to quit
  Codex completely and reopen it, then continue in a new task. Until a MagicPay
  tool actually answers, report that remaining step and nothing more — an
  installed plugin, a command that exited zero, or a browser window that opened
  is not evidence of a connection.
- Treat the current or refreshed catalog as choice-ready only when
  `begin_request_session`, `request_choice`, `decide_request`, and `wait_request`
  are callable. Follow [choices.md](choices.md): use a native choice interface
  when the current Codex host exposes one, otherwise relay `chatMessage`; also
  keep the returned MagicPay widget or options link attached to the same request.

### Verify and disconnect

- Browser work uses Codex's available native browser API and its documented
  input calls, including a host REPL wrapper where exposed. Keep the exact tab
  and MagicPay continuation identities; do not assume a generic sensitive-fill
  method exists. If the required native capability is unavailable, report it
  as blocked rather than switching to a MagicPay-owned browser.
- A source build or plugin reinstall does not prove this task loaded the new
  skill and tool descriptions. Verify catalog/build provenance after refresh;
  use a fresh task for clean installed-guidance acceptance when needed. Reuse
  completed OAuth rather than repeating setup.

- `codex mcp list` shows the `magicpay` connection and its auth status.
- Disconnect: `codex mcp logout magicpay`. Remove:
  `codex plugin remove magicpay@<marketplace>`. Both are host-local and revoke
  nothing remotely.

## Runtime Setup: Claude Code

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Claude Code commands; the canonical instructions stay host-neutral.

### Install

- Development channel: `claude plugin marketplace add https://github.com/nuanu-ai/skills.git#staging`
  registers marketplace `nuanu-skills-staging` (skip when it is already
  registered), then `claude plugin install magicpay@nuanu-skills-staging`.
- Production channel, after promotion: `claude plugin marketplace add nuanu-ai/skills`,
  then `claude plugin install magicpay@nuanu-skills`.
- Keep exactly one installed `magicpay@…` selector. `claude plugin list --json`
  shows the installed selector, version, and bundled MCP server.

### Connect

- The bundled connection is named `plugin:magicpay:magicpay`.
- Interactive session: the user runs `/mcp`, selects `magicpay`, and
  authenticates; the secure browser OAuth window opens from there.
- Terminal: `claude mcp login plugin:magicpay:magicpay`. It requires an
  interactive terminal. An agent shell without one cannot start the login and
  must hand off to the user instead of trying workarounds.
- Manual handoff when no Connect action can be initiated:
  **/mcp → magicpay → Authenticate**.

### Catalog refresh

- After OAuth, discover and call `get_magicpay_capabilities` in this session
  first. If it succeeds, continue here. If the needed tools remain unavailable,
  run `/reload-plugins` or start a new session. Neither repeats OAuth, and
  neither is the action that opens OAuth.
- Treat the refreshed catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Use them
  with the exact choice loop in [choices.md](choices.md).

### Native choice presentation

- In an interactive parent session where `AskUserQuestion` is available, a new
  `waiting_user` result with two to four stored options may use one
  `AskUserQuestion` single-select picker instead of echoing
  `structuredContent.chatMessage`. This is the chat presentation; also expose
  the MagicPay widget or returned `request_url`. Use the exact returned
  `request.spec.prompt`, preserve the stored option order, and build labels and
  descriptions only from returned `request.spec.options`; never use the
  caller-local input. If the exact stored titles cannot be represented
  unambiguously within the host's option-label limits, use the unchanged
  `chatMessage` fallback.
- `AskUserQuestion` is presentation-only. Keep an exact label-to-opaque-ID map,
  show either the picker or `chatMessage`, never both chat variants, and do not create a
  second MagicPay request. After a valid selection, call `decide_request` with
  `decision: 'confirmed'` and that exact `selectedChoiceId` on the same
  `sessionId` and `requestId`, then call `wait_request` on those same IDs.
- Before presenting anything, use the canonical chat fallback for five to eight
  options, an unavailable native picker, a subagent, or an option set the native
  control cannot represent unambiguously.
- After `AskUserQuestion` has shown the active choice, an explicit cancel or
  denial entered through host-added **Other** is submitted as `decision:
  'denied'` on the same MagicPay request and followed by `wait_request`. Any
  other unmapped, free-text, or **Other** answer never switches to `chatMessage`;
  correct it by presenting the same stored options through `AskUserQuestion`
  again. Do not mutate the request or create a sibling request.

### Verify and disconnect

- `claude mcp get plugin:magicpay:magicpay` reports `Connected` after OAuth.
- Disconnect: `claude mcp logout plugin:magicpay:magicpay`. Remove:
  `claude plugin uninstall magicpay@<marketplace>`. Both are host-local and
  revoke nothing remotely.

## Runtime Setup: OpenClaw

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names OpenClaw commands; the canonical instructions stay host-neutral.

### Install

- Install the `magicpay` plugin through OpenClaw's native plugin manager. Its
  `openclaw.plugin.json` declares both the skill and the `magicpay` Streamable
  HTTP MCP server with `auth: oauth`; do not add a second MCP entry by hand.

### Connect

- `openclaw mcp login magicpay` opens the secure browser OAuth window and keeps
  the token in OpenClaw's owner-only store.
- Manual handoff when no Connect action can be initiated: run
  `openclaw mcp login magicpay`.

### Catalog refresh

- `openclaw mcp reload`. If tools still do not appear, publish the Gateway
  configuration or restart the process that owns the MCP clients. Neither
  repeats OAuth.
- Treat the refreshed catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md), including both chat and the MagicPay link/widget.

### Verify and disconnect

- `openclaw mcp doctor --probe` or `openclaw mcp status` confirms the
  connection.
- Per-requester OAuth links belong in private conversations; keep group-chat
  payment disabled unless the payer identity is private to the requester.
- Disconnect through OpenClaw's MCP connection management; it is host-local
  and revokes nothing remotely.

## Runtime Setup: Grok Build

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Grok Build commands; the canonical instructions stay host-neutral.

### Install

- Grok Build loads marketplace sources from `[[marketplace.sources]]` entries in
  `~/.grok/config.toml` (fields `name`, `git`, optional `branch`, or `path`) and
  from `extraKnownMarketplaces` in `~/.grok/settings.json`. Inspect those before
  adding anything: exactly one `magicpay` plugin may remain installed, so remove
  any other first.
- Prefer the user's own `/plugins` modal inside the running app, Marketplace
  tab. An install made there rebuilds that app's plugin runtime; editing the
  configuration files underneath reaches only sessions started afterwards.
- Development channel: add a source with
  `git = "https://github.com/nuanu-ai/skills.git"` and `branch = "staging"`,
  or run `grok plugin marketplace add https://github.com/nuanu-ai/skills.git`
  and select the `staging` branch, then install `magicpay` from that source.
- Production channel, after promotion: `grok plugin marketplace add
  nuanu-ai/skills`, then install `magicpay` from the `/plugins` Marketplace tab.
- The `/skills` and `/mcps` modals show what the app actually loaded. Treat them
  as the source of truth over any file you edited.

### Connect

- Use the plugin's Connect action. Grok Build performs dynamic client
  registration against the MagicPay OAuth server, registering itself as `Grok`
  with the loopback redirect `http://127.0.0.1:{port}/callback`, and it parses
  the RFC 9207 `iss` response parameter. No pre-issued client id is required.
- Tokens are stored by the host in `~/.grok/mcp_credentials.json`. Never read,
  copy, print, or pass that file's contents; connection management belongs to
  the host.
- Wait for the authorization to finish on its own and poll
  `get_magicpay_capabilities` until it answers. Do not ask the user to confirm
  the secure window in chat.
- Manual handoff when no Connect action can be initiated: the `/mcps` modal,
  `magicpay`, then its Authenticate action.

### Catalog refresh

- After OAuth, probe this task's callable catalog for
  `get_magicpay_capabilities`, including any deferred or lazy tool discovery.
  Continue setup and any retained request in the same task once the call
  succeeds. A tool missing from the initially shown list is not evidence that it
  is unavailable.
- Only when an actual lookup cannot discover or call a required MagicPay tool
  should you say the catalog is stale. Reopen the `/mcps` modal so the host
  reuses the completed OAuth; that is a catalog fallback, not a reason to repeat
  authorization.
- When a reopened modal still cannot discover the tools, this app never loaded
  the plugin: the install landed in a different process. Ask the user to quit
  Grok Build completely and reopen it. Until a MagicPay tool actually answers,
  report that remaining step and nothing more. An installed plugin, a command
  that exited zero, or a browser window that opened is not evidence of a
  connection.
- Treat the catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md): use a native choice interface when this host exposes
  one, otherwise relay `chatMessage` and keep the returned MagicPay widget or
  options link attached to the same request.

### Payment rails on this host

- Grok Build has no browser. The agent-direct browser checkout rail is
  unavailable here, so never start `run_browser_payment`, never create a
  checkout session expecting a live tab, and never ask the user to paste card
  values as a substitute.
- Use the x402 and crypto transfer rails, which need no browser. When a request
  can only be completed in a browser, report that limitation and stop rather
  than switching to another controller.

### Verify and disconnect

- The `/mcps` modal shows the `magicpay` connection and its authorization state.
- Disconnect through that modal, and remove the plugin through `/plugins`. Both
  are host-local and revoke nothing remotely.
- A source build or plugin reinstall does not prove this task loaded the new
  skill and tool descriptions. Verify catalog provenance after a refresh, and
  reuse completed OAuth rather than repeating setup.

## Runtime Setup: Grok Bot

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Grok Bot surfaces; the canonical instructions stay host-neutral.

### Install

- Grok Bot follows the Cursor account's plugin and MCP policy and discovers
  plugins under **Settings → Plugins**. Exactly one `magicpay` plugin may remain
  installed; remove any other first.
- Preferred channel: install MagicPay from the Cursor Marketplace listing
  through **Settings → Plugins**. The plugin bundles this skill, the remote
  MagicPay MCP declaration, and its pre-registered public OAuth client, so the
  host skips dynamic client registration.
- Interim channel while the listing is pending: add the remote MagicPay MCP as
  a custom remote server; the host shows it as `user-magicpay`. Do not install a
  MagicPay CLI, a local server, or a second browser controller.
- Team or enterprise accounts inherit the team's Cursor plugin policy; there are
  no separate Grok Bot plugin controls. When the policy blocks the install,
  report that blocker and stop.

### Connect

- Use the plugin's Connect action. MCP authentication is shared across the
  Cursor account, so one MagicPay connection serves every Bot on that account;
  never connect per Bot, and keep payment writes off for team or group use
  until identity isolation is confirmed.
- The Connect action opens the same secure browser OAuth flow: email and OTP
  are entered in that window, never in chat. The host redirects to
  `http://localhost:8787/callback` on desktop and to
  `https://www.cursor.com/agents/mcp/oauth/callback` for web and cloud agents.
- Wait for the authorization to finish on its own and poll
  `get_magicpay_capabilities` until it answers. Do not ask the user to confirm
  the secure window in chat.
- Manual handoff when no Connect action can be initiated:
  **Settings → Plugins → MagicPay → Connect**.

### Catalog refresh

- After OAuth, probe this task's callable catalog for
  `get_magicpay_capabilities`, including any deferred or lazy tool discovery.
  Continue setup and any retained request in the same task once the call
  succeeds. A tool missing from the initially shown list is not evidence that it
  is unavailable.
- Only when an actual lookup cannot discover or call a required MagicPay tool
  should you say the catalog is stale. Start a new Bot task so the host reuses
  the completed OAuth; that is a catalog fallback, not a reason to repeat
  authorization.
- When a new task still cannot discover the tools, ask the user to restart the
  Grok Bot app. Until a MagicPay tool actually answers, report that remaining
  step and nothing more. An installed plugin, a Connect window that opened, or
  a completed OAuth redirect is not evidence of a connection.
- Treat the catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md): use a native choice interface when this host exposes
  one, otherwise relay `chatMessage` and keep the returned MagicPay widget or
  options link attached to the same request.

### Approvals and the shared browser

- Grok Bot's browser is a persistent cloud computer shared by every Bot on the
  account. Purchases stay behind the host's own **Require Approval** rule. The
  Bot asks once, at the exact approved final click, after MagicPay approval and
  card fill. Never tell the user to add an "Always Allow" rule for purchases.
- The card returned after finalized MagicPay approval is MagicPay's single-use
  credential for that exact approved checkout. Entering it is the approved
  payment step, not a password, passkey, or two-factor code. Supply it only
  through the host's typed sensitive-fill input for the approved tab, and keep
  it out of chat, scripts, files, logs, and evidence.
- Decline any browser or merchant prompt to save the card, because the
  computer is shared.
- If the host still demands a human takeover at card entry, stop: put nothing in
  chat, record `click_uncertain` or `not_clicked` exactly as observed with
  `record_browser_payment_result`, and let MagicPay reconcile. Bank and
  merchant challenges are ordinary takeover steps; MagicPay's own approval OTP
  keeps its existing chat path.
- Never replay a click, never substitute another payment route, and never read
  card values into a script or a shell argument.

### Verify and disconnect

- **Settings → Plugins → MagicPay** shows the connection and its authorization
  state.
- Disconnect and remove through that same panel. Both are host-local and revoke
  nothing remotely.
- A plugin reinstall does not prove this task loaded the new skill and tool
  descriptions. Verify catalog provenance in a new task, and reuse completed
  OAuth rather than repeating setup.
