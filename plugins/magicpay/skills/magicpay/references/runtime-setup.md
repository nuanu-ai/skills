# Runtime Setup: MagicPay host plugin

This plugin serves Codex, Claude Code, and OpenClaw. Use only the section
for the host you are running in; the canonical instructions in
[setup.md](setup.md) never name a host.

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
