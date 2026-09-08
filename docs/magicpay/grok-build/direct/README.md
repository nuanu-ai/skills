# Install MagicPay for Grok Build

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Development channel: add a `[[marketplace.sources]]` entry to `~/.grok/config.toml` with `git = "https://github.com/nuanu-ai/skills.git"` and `branch = "staging"`, or run `grok plugin marketplace add https://github.com/nuanu-ai/skills.git`, then install `magicpay` from the `/plugins` Marketplace tab.
- Production channel, after promotion: `grok plugin marketplace add nuanu-ai/skills`, then install `magicpay` from the `/plugins` Marketplace tab.
- Connect through `/mcps` → `magicpay` → Authenticate; the host registers itself as `Grok` with a loopback redirect. Reopen `/mcps` if tools do not appear, then quit and reopen the app.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Confirm get_magicpay_capabilities reports client_browser mode.

Supported skill directories:

- skills/
- ~/.grok/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.13
