# Install MagicPay for OpenClaw

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Install the `magicpay` plugin through OpenClaw's native plugin manager; its `openclaw.plugin.json` declares the skill and the OAuth MCP server.
- Connect with `openclaw mcp login magicpay`; verify with `openclaw mcp doctor --probe`.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Confirm get_magicpay_capabilities reports client_browser mode.

Supported skill directories:

- skills/
- ~/.openclaw/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.4
