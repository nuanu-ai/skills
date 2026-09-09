# Runtime Setup: MagicPay host plugin

This plugin serves Codex, Claude Code, OpenClaw, Grok Build, and Grok Bot. Use
only the section for the host you are running in; the canonical instructions
in [setup.md](setup.md) never name a host.

## Runtime Setup: Codex

Host actions for the universal flow in [setup.md](setup.md). Codex's own
plugin manager is not the retired MagicPay CLI. An existing supported `codex`
command may inspect plugins, bootstrap the requested channel's installation, and start
host-managed OAuth when native actions are unavailable and host policy permits
it. Use the same local host and configuration scope as this app; do not change profiles, install or upgrade a
CLI, hunt for an app-bundled binary, or launch a private App Server client.
The retired MagicPay CLI remains abandoned. The supported Codex OAuth command
below opens the same secure MCP sign-in page; it does not collect credentials.

### Install

- First discover current-task MagicPay tools, including deferred discovery.
  When `get_magicpay_capabilities` is callable, verify the requested environment
  and authenticated readiness through [setup.md](setup.md); do not require a
  separate inventory action or reinstall a working connection. An auth challenge
  goes to Connect; a service failure is not evidence that the plugin is absent.
- When tools are unavailable, inspect native plugin inventory or, when that
  action is unavailable, use the existing host's `codex plugin list --json` and
  `codex plugin marketplace list --json`. Do not create a duplicate `magicpay`
  plugin or connection. Report existing duplicates; do not remove them unless
  that cleanup or channel change is requested.
  If the requested selector and endpoint are correct, check readiness first;
  do not reinstall or recreate MCP configuration. Repeat OAuth only for an
  explicit sign-in request or an observed authentication requirement, following Connect below.
- If neither inventory route can be read and state has not otherwise been
  established, installation and auth state remain unknown. Missing MagicPay
  tools or inventory access do not erase a confirmed installation or completed
  OAuth. Reuse that evidence and proceed to Catalog refresh. For unknown state,
  hand off **Plugins → MagicPay** to inspect the existing plugin details:
  install only if absence is confirmed. Report connection state as unknown
  until it can be inspected; do not invent a Connect button.
- Only when installation is needed, use an actually callable, eligible native
  install action when exposed, or the permitted commands for the requested channel below.
  The agent runs supported commands; it does not merely print them for the user.
  Never automate Codex's own UI, invent a native action, or bypass a refused
  approval through another route.
- For development, the trusted source is `nuanu-ai/skills` at ref `staging`;
  public-directory listing is not required. When installation is needed, add a
  missing source with `codex plugin marketplace add nuanu-ai/skills --ref staging`.
  If already registered, verify its source and development channel; refresh only
  that source when needed with `codex plugin marketplace upgrade nuanu-skills-staging`.
  Install the confirmed absent plugin with `codex plugin add magicpay@nuanu-skills-staging`.
  Do not refresh or reinstall a correct existing plugin merely because a newer
  release is available. Stop on a source/channel mismatch instead of replacing it.
- For production after promotion, the trusted source is `nuanu-ai/skills` at ref
  `main`. When installation is needed, register a missing source with
  `codex plugin marketplace add nuanu-ai/skills --ref main`. If already registered,
  verify `nuanu-skills` has that source and ref before refreshing only that source
  when needed with `codex plugin marketplace upgrade nuanu-skills`. Install only
  the absent plugin with `codex plugin add magicpay@nuanu-skills`.
  Public-directory listing is not required. Preserve a correct installed version
  even if a newer release exists; stop on a source/channel mismatch. Production
  setup does not authorize switching or duplicating an existing development
  installation; preserve it and report the channel mismatch.
- For an explicitly requested repo-local development install, verify the repository
  root and its marketplace first. Register it only if absent using
  `codex plugin marketplace add <verified repository root>`, then install the
  absent `magicpay@agentpay-local` with `codex plugin add magicpay@agentpay-local`.
  Substitute the actual verified path; do not run the placeholder literally.
- For an actual channel change, use native Disconnect for the old exact bundled
  connection before native Remove for its MagicPay plugin.
  Remove only the obsolete MagicPay selector; preserve marketplace registrations,
  unrelated plugins, and remote state. Do not add a second MCP server.
- If the existing command is missing, unsupported, or targets another host/profile,
  report that limit without installing a different executable. A policy or approval
  denial stops the attempt; never switch routes to bypass it.
- Manual handoff when installation is needed and neither permitted route is available:
  **Plugins → MagicPay → Install**, choosing the requested channel.
  Setup is waiting for that action; installation alone is not readiness.
  A successful plugin command proves only its reported local installation,
  not activation in this running app, authentication, or usable tools.

### Connect

- Requested sign-in: when the user explicitly asks to open the MagicPay login
  window, sign in again, or reconnect, start one host-managed OAuth attempt for
  the exact installed MagicPay connection in this same chat, even when inventory
  reports `o_auth`. This explicit request supplies the sign-in requirement for
  the native action or supported Codex command below; do not require a failed
  tool call first or send the user to Settings while that route is available.
  Reuse an already pending login; do not open a second flow. A repeated setup
  prompt alone is not an explicit sign-in request: preserve working authorization.
  A canceled or denied action stops the current attempt; never restart it
  automatically. Apply each host policy to its exact host and connection scope;
  an unrelated connector restriction does not prohibit MagicPay login.
- Initial sign-in: if tools are unavailable after discovery and you just installed the
  exact plugin during this authorized setup and have no evidence of a completed sign-in
  or a working authenticated connection, start one host-managed OAuth attempt in this
  installation chat using the eligible native action or supported Codex command below.
  Do not wait for missing tools to request authentication. The inventory value `unknown`
  means undetermined, not an authentication rejection; it does not block this first
  sign-in or require a Settings/new-chat handoff. A cached `o_auth` inventory label
  alone does not establish either and must not skip initial login after confirmed plugin
  absence. A plugin install does not itself complete OAuth. Check for a pending login
  first and wait for that same attempt; a canceled, denied, or failed login must not be
  restarted. Preserve completed authorization confirmed by the user or host and use
  authenticated tools or catalog recovery; unknown or missing tools alone never justify
  repeating OAuth. A reported network/service failure keeps its own recovery path. Keep
  the one login process attached until host-reported completion or failure, then
  discover tools and verify readiness here.
- When a MagicPay tool is callable, the primary path is the native MCP connection
  prompt triggered by calling `get_magicpay_capabilities` once through the
  current task's MagicPay tool.
  Tool definitions can be discoverable before sign-in; that does not mean the
  account is authenticated. A missing or expired authorization returns
  `mcp/www_authenticate`, which lets the app display its native Connect or
  Reconnect prompt. This is native OAuth and needs no separate Connect tool.
  A failed startup can prevent tools from loading, so this prompt cannot repair
  every connection before initialization.
  Never reproduce that tool call with curl, a shell MCP client, or private RPC:
  the result must pass through the current host's tool channel to trigger its UI.
- When tools cannot load, inspect the existing connection's supported host
  status. `status=failed` with `failureReason=reauthenticationRequired` is an
  authentication failure; follow this Connect recovery, not the new-chat
  activation fallback. A current OAuth invalid_grant rejection for that exact
  connection also requires authentication recovery. An installed/enabled plugin does not prove accepted
  authentication. The `o_auth` label is saved OAuth metadata, not proof of
  accepted authentication or readiness. Missing tools alone do not establish
  that sign-in is required. A service or network failure keeps its own recovery
  category; do not reset credentials or suggest a new task to fix it.
- For initial sign-in or when the exact installed connection requires sign-in,
  use one eligible native
  Authenticate/Connect action when callable for the exact installed `magicpay`
  server in this host. Reuse an already pending login instead of opening a
  second prompt or sending the user to Settings. Let the user approve the
  displayed prompt and enter email and OTP in the secure window. Wait for
  host-reported completion before rediscovering tools and verifying readiness
  here; an open or closed window is not completion.
- If no eligible native action is exposed, initial sign-in or an observed
  authentication requirement applies, and host policy permits the command,
  the agent runs the existing host's
  `codex --version`, then `codex mcp login magicpay` once in the same host and
  configuration scope. Require Codex 0.147.0 or newer: 0.146.1 drops the callback
  issuer. If the version is older, unknown, or the command is unsupported, report
  the host limitation; do not install/upgrade a CLI or hunt for another binary.
  This supported Codex command launches host-managed OAuth. The bans on the
  retired MagicPay CLI and raw shell MCP clients do not prohibit it.
  Missing native controls do not establish a policy denial. Apply current
  instructions and observed host decisions; historical notes do not establish
  a current restriction. If a policy blocks the fallback, identify the applicable
  source or observed denial without exposing private instructions; do not invent
  a session-policy prohibition. Denial or cancellation of the current login
  attempt stops that attempt; never switch routes to bypass it. A restriction limited to agent
  commands may still permit the documented manual native handoff.
- Let Codex open its host-issued authorization URL in the secure browser.
  If browser handoff is needed, use that exact URL with the host browser without
  echoing it into chat; do not reconstruct a callback, relay its parameters, or
  handle email, OTP, codes, or tokens. Keep OAuth URLs out of transcript output.
  Capture command output without echoing it; report only sanitized completion
  or error status.
  Wait for this one host command to finish, then rediscover tools in this task.
  The agent runs this permitted command; the user enters no commands.
- When either initial sign-in or an observed authentication requirement applies, and neither an
  eligible native action nor the permitted command is available, report the observed host limitation and give one manual native handoff for
  the existing `magicpay` server: **Settings → MCP servers → Authenticate**.
  Preserve the installed plugin and selected environment. Say the screen
  appeared only when observed; this handoff is waiting for the user's action,
  not completed authentication or an agent-triggered native control.
  Documented App Server methods such as `mcpServer/oauth/login` are host
  integration interfaces, not callable model tools. Do not invent a Connect
  tool, invoke private RPC, automate the host's settings, launch another App
  Server process, or add a duplicate MCP server to bridge the missing control.
- Cancellation, denied approval, or a login failure stops this attempt; preserve
  the installed plugin and report the failed phase instead of repeating OAuth.
  Reauthentication does not approve a payment. After readiness succeeds,
  continue the retained request or read and continue its exact existing payment
  operation with its required approval; do not create a replacement purchase.

### Catalog refresh

- After existing or newly completed authorization, use Codex's
  current-task deferred tool discovery to find and call
  `get_magicpay_capabilities`. Continue setup and any retained request
  in this task when the call succeeds. A tool omitted from the initial visible
  list is not evidence that it is unavailable. A successful MagicPay call must
  not be followed by a **New task** instruction.
- If an actual current-task lookup cannot discover or call a required tool,
  inspect the installed/enabled state and host auth/service result.
  Use one supported native reload only when actually exposed, then rediscover
  when the host applies it, including the next turn in this task when supported.
  Do not build a private App Server client or launch another process as a
  substitute for reloading this app. Missing tools alone do not identify the
  cause; distinguish auth errors, service failures, and unavailable tools.
  An observed `reauthenticationRequired` result returns to Connect; a supported
  installed version or saved OAuth metadata does not override that failure.
- If the correct plugin is installed and enabled, OAuth completed, and tools
  remain unavailable without a separate observed auth or service error, give
  one new-chat handoff. Do not require an explicit host instruction to offer
  this recovery. If installation or auth state is unknown, say so instead of
  claiming the user is signed in. Pending, canceled, denied, or failed OAuth
  follows Connect above; opening a new chat does not complete authentication.
- For development, say:

  > MagicPay Development is installed and signed in, but its tools aren't
  > available in this chat. Open a new Codex chat and send:
  >
  > **Verify my MagicPay development connection. I already installed and signed
  > in, then opened this new chat because the tools were missing.**
  >
  > Keep the existing installation and sign-in; no setup steps need repeating.

  Use the requested environment in the message and prompt. Keep the recovery
  cue so the next chat knows this step was already attempted. If that chat
  still cannot load tools, report tool loading blocked without suggesting
  another chat, reinstall, restart, or sign-in. Handle any actual new auth or
  service error separately; missing tools alone do not justify repeating OAuth.
- In the new chat, discover tools and verify capabilities, authenticated status,
  and balance through [setup.md](setup.md), reusing the installation and saved
  authorization. The handoff is activation recovery, not single-prompt success.
  Readiness requires successful capability and authenticated status calls,
  followed by the balance read; use the reference's unavailable-balance branch
  if that read fails. An installed plugin or OAuth completion alone is not readiness.
- A new chat does not inherit the old request or payment approvals. If useful,
  include the original non-sensitive intent in the continuation prompt, keeping
  the requested environment and recovery cue. Act only on the new chat's request;
  do not copy credentials or imply permission to replay an existing operation.
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
- For a pre-dispatch `.fill()` clipboard or focus error, follow the targeted
  recovery in [host-browser-payments.md](host-browser-payments.md). When exposed
  by the current host, a frame-scoped locator's `pressSequentially` provides
  native per-key input after re-observing the intended field.
  If observed partial text needs replacement, use that locator's documented
  `press('ControlOrMeta+A')` before entering the replacement. Empty fields need
  no clearing. `.type()` may share `.fill()`'s clipboard path; do not assume it
  is an independent recovery method.
- A source build or plugin reinstall does not prove this task loaded the new
  skill and tool descriptions. Verify the requested selector, endpoint, and
  supported version; record observed build revisions without demanding equality
  between independently deployed services. Reuse completed OAuth.

- Inspect connection status through native plugin/MCP controls and authenticated
  tools. Disconnect or remove MagicPay through its native plugin controls only
  when requested; preserve unrelated integrations and all remote payment state.

## Runtime Setup: Claude Code

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Claude Code commands; the canonical instructions stay host-neutral.

### Install

- First discover current-task MagicPay tools, including deferred tools. When
  `get_magicpay_capabilities` is callable, verify the requested environment and
  authenticated readiness through [setup.md](setup.md), without requiring
  separate inventory. An auth challenge goes to Connect; a service failure is
  not proof of absence. Neither calls for reinstalling.
- Otherwise inspect `claude plugin list --json` and
  `claude plugin marketplace list --json` in the same host and configuration
  scope. Use plugin details or `/mcp` to inspect the bundled connection.
  Preserve an already-correct selector, endpoint, supported installed version,
  and authorization; repeated setup does not request a reinstall or another login.
- If inventory is unavailable, state is unknown: hand off `/plugin` to inspect
  details. Install only if absent; Authenticate only when needed. Use existing
  supported host commands; if unavailable, use the native control instead of
  installing another CLI. A denied host action stops the attempt; do not switch
  routes to bypass it. Report existing duplicates; do not remove them
  automatically or create another connection.
- When development installation is needed, verify any existing
  `nuanu-skills-staging` source is `nuanu-ai/skills` at ref `staging`. Stop on a
  source/channel mismatch. Register only a missing source with
  `claude plugin marketplace add https://github.com/nuanu-ai/skills.git#staging`,
  then install only the absent plugin with
  `claude plugin install magicpay@nuanu-skills-staging`.
  Public-directory listing is not required.
- Production channel, after promotion: verify the existing `nuanu-skills`
  source is the stable `nuanu-ai/skills` marketplace. Register only a missing
  source with `claude plugin marketplace add nuanu-ai/skills`, then install
  only the absent plugin with `claude plugin install magicpay@nuanu-skills`.
  Stop on a source/channel mismatch.
- For explicitly requested repo-local installation, verify the repository root
  and its `agentpay-local` marketplace. Register only a missing source with
  `claude plugin marketplace add <repository root>`, substituting the actual
  verified path as one quoted argument; never run the placeholder literally.
  Install only the absent plugin with `claude plugin install magicpay@agentpay-local`.
  Stop on a source/channel mismatch.
- Check the interactive in-session installation summary. If it reports the
  plugin is active, continue.
  A shell install or a summary requesting activation needs the user to enter
  `/reload-plugins` in this same conversation before `/mcp` can authenticate the
  newly loaded server. This is a user command, not a shell or model reload API.
- If reload stops with a prompt-cache warning, explain that the next request
  may re-read the conversation. The user can accept that cost with
  `/reload-plugins --force`; do not use `--force` without that warning.
- Install success is not authentication or same-session readiness.

### Connect

- The bundled connection is named `plugin:magicpay:magicpay`.
- Reuse existing authorization. When sign-in is needed, the user runs `/mcp`,
  selects `magicpay`, and authenticates; the secure browser OAuth window opens
  from there.
- Terminal: `claude mcp login plugin:magicpay:magicpay`. It requires an
  interactive terminal. An agent shell without one cannot start the login and
  must hand off to the current session instead of creating a PTY workaround.
- Manual handoff when no Connect action can be initiated:
  **/mcp → magicpay → Authenticate**.

### Catalog refresh

- After OAuth, discover and call `get_magicpay_capabilities` in this session
  first. If it succeeds, continue here. If the needed tools remain unavailable,
  have the user run `/reload-plugins` in the same conversation, following the
  cache-warning rule above, then discover again. Preserve completed OAuth.
- Reconnect through `/mcp` only for an observed connection failure. If reload
  fails or tools remain unavailable, report the observed phase and error.
  A user reload is same-chat recovery, not automatic single-prompt completion.
  Do not launch a separate `claude -p` probe: another process cannot verify this
  conversation's tool availability, and its account login does not diagnose
  the plugin's OAuth connection.
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

- Verify the actual MCP URL in the selected catalog entry, existing connector, or native
  Add request against this guide's endpoint before authentication. A matching name,
  logo, or tool count does not establish the channel; a production setup prompt does not
  turn an existing development connector into production. If the catalog URL is wrong or
  unavailable, do not install or authenticate that unverified channel. For a fresh setup
  with confirmed connector absence, use the host's supported native custom remote MCP
  Add action with the exact endpoint from the requested guide. The setup prompt already
  selects production or development: initiate that native Add/Connect flow without a
  separate how-to-connect choice. Keep the host's required confirmation, including a
  question widget when required, and secure OAuth steps; confirm the exact action rather
  than offering an unverified listing anyway. If custom remote Add is unavailable,
  report that specific host limitation instead of inventing an action or asking the user
  to choose an unknown channel. An ordinary setup prompt does not request switching or
  duplicating an existing connection; preserve it until the user explicitly requests a
  channel change.
- Inspect the installed MagicPay connector and selected channel first; preserve
  a correct installation and valid authorization. Connectors are account-wide.
- If inventory is unavailable, open or hand off **Settings → Plugins → MagicPay**
  to inspect the connector details. Missing tools do not prove absence. Use
  **Connect** for an installed connector that needs sign-in; **Add** only when
  absent. Reuse a connected connector and discover its tools in this chat.
- When installation is needed, use the native MagicPay **Add** card when available. Manual fallback:
  **Settings → Plugins → MagicPay → Add**. Use the exact requested channel; a
  custom remote entry is an option only when the host supports it. Do not add a
  duplicate connection or install a MagicPay CLI or local server.
- If the requested connector/channel is unavailable or policy blocks Add,
  report that installation limit. The account's relationship with Cursor does
  not prove which OAuth client metadata this connection uses; do not infer Bot
  identity, client registration, or callback URLs from a generic Cursor label.

### Connect

- Use the native **Connect** action only when authentication is needed. Reuse
  the existing account-wide connection; do not authenticate separately per Bot.
  Keep payment writes off for team or group use until identity isolation is
  confirmed.
- The Connect action opens the same secure browser OAuth flow: email and OTP
  are entered in that window, never in chat. Let the host handle its registered
  callback and return to the original chat after authorization.
- Wait for host completion when observable, then discover
  `get_magicpay_capabilities` in that chat. An opened or closed browser window
  is not connection evidence; do not require a "done" reply.
- Manual handoff when no Connect action can be initiated:
  **Settings → Plugins → MagicPay → Connect**.
  Promise to open the email/OTP window only when the action is actually callable.

### Connection startup failure

- If Connect shows `Retry`, reports `unreachable`, or returns a registration or
  authentication error before the window opens, report connection
  startup failed and inspect the connector details. Do not diagnose email/OTP
  or blame the host or service from that label alone. Do not describe a failed
  card as a pending email/OTP prompt.
- For a developer handoff, collect the app version, UTC attempt time, requested
  endpoint origin/path, and the host's HTTP status or DNS/TLS/timeout error and
  request ID when available. A successful request from another machine does
  not verify the Bot connector's route. Keep credentials, account identifiers,
  and callback query values out of diagnostics.
- Preserve the installed connector. Do not repeat Add, reset the Bot computer,
  or restart OAuth automatically to work around an unexplained failure.

### Catalog refresh

- After OAuth, probe this task's callable catalog for
  `get_magicpay_capabilities`, including any deferred or lazy tool discovery.
  Continue setup and any retained request in the same task once the call
  succeeds. A tool missing from the initially shown list is not evidence that it
  is unavailable.
- If the connected connector is not available to this chat, the user can type
  `@` and attach MagicPay here, then retry discovery. Request this only when
  attachment is needed; it is a manual same-chat fallback, not automatic setup.
- For a reported connection failure, inspect the connector's detail page and
  follow its authentication action only when required, then return to this chat.
  Do not prescribe a new Bot task or an app restart for missing tools alone.
  If tools remain unavailable, report the observed connection or tool-loading
  limit. Installation and OAuth completion do not establish readiness.
- Treat the catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md): use a native choice interface when this host exposes
  one, otherwise relay `chatMessage` and keep the returned MagicPay widget or
  options link attached to the same request.

### Approvals and the shared browser

- MagicPay's own approval (its approval page, Telegram, or the narrow OTP path)
  is the payment approval, and it is the only one. Never add another: do not
  ask in chat whether to proceed before starting the payment run, do not ask
  again after MagicPay records the approval, and do not re-present an approval
  the user already gave. The "connected service" card that Grok Bot's Auto
  Review shows for a MagicPay tool call is the host reviewing that tool call,
  not a payment decision: let the user answer it and continue without adding a
  chat confirmation before or after it.
- Grok Bot's browser is a persistent cloud computer shared by every Bot on the
  account. Purchases stay behind the host's own **Require Approval** rule; that
  single host prompt at the exact approved final click, after MagicPay approval
  and card fill, is the only confirmation left in the flow. Never tell the user
  to add an "Always Allow" rule for purchases.
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
  descriptions. Verify the selected channel, endpoint, supported version, and
  actual calls in this chat; keep build revisions diagnostic and reuse OAuth.
