# Install MagicPay for Grok Bot

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Verify the actual MCP URL in the selected catalog entry, existing connector, or native Add request against this guide's endpoint before authentication. A matching name, logo, or tool count does not establish the channel; a production setup prompt does not turn an existing development connector into production. If the catalog URL is wrong or unavailable, do not install or authenticate that unverified channel. For a fresh setup with confirmed connector absence, use the host's supported native custom remote MCP Add action with the exact endpoint from the requested guide. The setup prompt already selects production or development: initiate that native Add/Connect flow without a separate how-to-connect choice. Keep the host's required confirmation, including a question widget when required, and secure OAuth steps; confirm the exact action rather than offering an unverified listing anyway. If custom remote Add is unavailable, report that specific host limitation instead of inventing an action or asking the user to choose an unknown channel. An ordinary setup prompt does not request switching or duplicating an existing connection; preserve it until the user explicitly requests a channel change.
- Inspect **Settings → Plugins → MagicPay** first. If installed, preserve the connector and use Connect only when sign-in is needed. Use the native Add card only when absent; missing tools do not prove absence. If the requested channel is unavailable, report that limit without adding a duplicate connection.
- Connect opens the secure email/OTP flow and uses the account-wide connection. If Connect shows Retry, reports unreachable, or returns a registration/authentication error before the window opens, inspect the connector error and record the attempt time; do not call a failed card a pending email/OTP prompt; do not diagnose credentials or reset the Bot computer. After authorization, discover tools in the original chat and use the readiness sequence in `references/setup.md`.

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

Release: magicpay-v0.4.43
