# Install MagicPay for Grok Bot

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Inspect **Settings → Plugins → MagicPay** first. If installed, preserve the connector and use Connect only when sign-in is needed. Use the native Add card only when absent; missing tools do not prove absence. If the requested channel is unavailable, report that limit without adding a duplicate connection.
- Connect opens the secure email/OTP flow and uses the account-wide connection. If Connect reports unreachable before the window opens, inspect the connector error and record the attempt time; do not diagnose credentials or reset the Bot computer. After authorization, discover tools in the original chat and use the readiness sequence in `references/setup.md`.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Confirm get_magicpay_capabilities reports client_browser mode.

Supported skill directories:

- skills/
- ~/.cursor/plugins/local/magicpay/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.18
