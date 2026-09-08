# Install MagicPay for Codex

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- Inspect the native plugin inventory and bundled MCP first; preserve the requested installation and existing authorization. If inventory is unavailable, state is unknown: hand off **Plugins → MagicPay** to inspect details, then Connect if installed and sign-in is needed, or Install only if absent. Missing tools do not prove absence. Only when installation is needed, use an actually callable, eligible native install action, otherwise hand off **Plugins → MagicPay → Install** and wait for it. Never automate Codex itself or bypass a refused approval.
- Development channel: select `magicpay@nuanu-skills-staging` from `nuanu-ai/skills` at ref `staging` in the native plugin manager. If that source is unavailable, report the missing source.
- Production channel, after promotion: select `magicpay@nuanu-skills` from the `nuanu-ai/skills` marketplace at its stable ref.
- For a real channel change, use native Disconnect for the old exact connection before native Remove for its MagicPay plugin. Keep one plugin and bundled connection; preserve unrelated integrations and remote state.
- Only when authentication is missing, start one callable native Connect action. If none is exposed, hand off **Plugins → MagicPay → Connect** and wait for it. After host-reported completion, use current-task deferred discovery and the readiness sequence in `references/setup.md`. Use one supported native reload only when actually exposed. If still blocked, report the failed phase; manual activation is not single-prompt success. Do not loop through reinstall, OAuth, new tasks, or restarts.
- Setup has no external CLI dependency or terminal compatibility path. Do not find, install, upgrade, or invoke a CLI from PATH, an app bundle, or a private App Server client to manage this connection.

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

Release: magicpay-v0.4.18
