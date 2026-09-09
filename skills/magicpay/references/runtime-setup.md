# Runtime Setup: General Agent

Host-specific actions for the universal flow in [setup.md](setup.md). This
bundle is host-neutral, so every action here is expressed in the host's own
terms.

## Install

- Import the `magicpay` skill folder into the host's skill directory
  (`.agents/skills/` or `~/.agents/skills/`).
- Add the MagicPay remote Streamable HTTP MCP server through the host's native
  MCP settings with OAuth, using the MCP URL shown on the MagicPay setup page
  you installed from. Never paste a static API key or token.

## Connect

- Start the host's own MCP login or Connect action for `magicpay`; the secure
  browser OAuth window handles email and OTP.
- Manual handoff when no Connect action can be initiated: the host's MCP
  connection settings for `magicpay`.

## Catalog refresh

- Reload the host's MCP servers or start a new session. Neither repeats OAuth.
- Treat the refreshed catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md), including both chat and the MagicPay link/widget.

## Verify and disconnect

- Disconnect through the host's MCP connection management; it is host-local
  and revokes nothing remotely.
