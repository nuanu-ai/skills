# Install MagicPay for Claude Code

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Development channel: `claude plugin marketplace add https://github.com/nuanu-ai/skills.git#staging`, then `claude plugin install magicpay@nuanu-skills-staging`.
- Production channel, after promotion: `claude plugin marketplace add nuanu-ai/skills`, then `claude plugin install magicpay@nuanu-skills`.
- Connect with `/mcp` → `magicpay` → Authenticate, or `claude mcp login plugin:magicpay:magicpay` in a terminal. A session that was already open needs `/reload-plugins`.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Confirm get_magicpay_capabilities reports client_browser mode.

Supported skill directories:

- .claude/skills/
- ~/.claude/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.4
