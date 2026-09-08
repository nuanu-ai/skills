# Install MagicPay for Codex

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Inspect the installed selector and bundled MCP first; preserve the requested installation and existing authorization. Use an actually callable, eligible native install action, otherwise the agent runs permitted Codex commands. Never automate Codex itself or bypass a refused approval.
- When development installation is needed, register the trusted marketplace only if absent with `codex plugin marketplace add nuanu-ai/skills --ref staging`; otherwise verify its source and staging ref before refreshing it with `codex plugin marketplace upgrade nuanu-skills-staging`. Complete any channel change below, then run `codex plugin add magicpay@nuanu-skills-staging`.
- Production channel, after promotion: the `nuanu-ai/skills` marketplace at its stable ref, then `codex plugin add magicpay@nuanu-skills`.
- For a real channel change, log out the old exact connection before removing only its MagicPay selector. Keep one plugin and bundled connection; preserve unrelated integrations and remote state.
- Only when authentication is missing, start one callable native Connect action or permitted `codex mcp login magicpay`, wait for host completion, then use current-task deferred discovery and the readiness sequence in `references/setup.md`. Use one supported native reload only when actually exposed. If still blocked, report the failed phase; manual activation is not single-prompt success. Do not loop through reinstall, OAuth, new tasks, or restarts.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Confirm get_magicpay_capabilities reports client_browser mode.

Supported skill directories:

- .codex/skills/
- ~/.codex/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.14
