# Runtime Setup: Hermes

Host-specific actions for the universal flow in [setup.md](setup.md). Only this
file names Hermes commands; the canonical instructions stay host-neutral.

## Install

- Install the MagicPay skill from the Hermes hub payload, or place the
  `magicpay` skill folder in `skills/` or `~/.hermes/skills/`.
- Declare the remote MagicPay MCP in the Hermes configuration with native OAuth
  (`auth: oauth`) using the MCP URL shown on the MagicPay setup page you
  installed from. Never add an Authorization header or a static token.

## Connect

- `hermes mcp login magicpay` from a trusted local terminal opens the secure
  browser OAuth window. A Telegram or Discord gateway message cannot complete
  it.
- Manual handoff when no Connect action can be initiated: run
  `hermes mcp login magicpay` in the terminal.

## Catalog refresh

- `/reload-mcp` in the running session reloads MCP tools; `/reload-skills`
  reloads an updated skill. Neither repeats OAuth.
- Treat the refreshed catalog as choice-ready only when `begin_request_session`,
  `request_choice`, `decide_request`, and `wait_request` are callable. Follow
  [choices.md](choices.md), including both chat and the MagicPay link/widget.

## Verify and disconnect

- Use one payer identity per isolated Hermes profile; a shared multi-user
  gateway must not share one MagicPay connection.
- Disconnect through Hermes' MCP connection management; it is host-local and
  revokes nothing remotely.
