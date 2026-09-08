# Install MagicPay for Grok Bot

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Install MagicPay from **Settings → Plugins** once the Cursor Marketplace listing is live; until then add the remote MagicPay MCP as a custom remote server (shown as `user-magicpay`).
- Connect through the plugin Connect action; MCP authentication is shared with the Cursor account and opens the same browser OAuth flow.

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

Release: magicpay-v0.4.17
