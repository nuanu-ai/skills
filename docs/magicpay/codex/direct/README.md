# Install MagicPay for Codex

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- First discover current-task MagicPay tools, including deferred tools. When `get_magicpay_capabilities` is callable, verify the requested environment and authenticated readiness without requiring separate inventory. An auth challenge goes to native Connect; a service failure is not proof of absence. Otherwise inspect native inventory or the existing host's `codex plugin list --json` and `codex plugin marketplace list --json` when permitted. Preserve a correct installation and authorization. If neither inventory route is available, state is unknown: hand off **Plugins → MagicPay** to inspect details; Connect if installed and sign-in is needed, Install only if absent. Missing tools do not prove absence. Never automate Codex itself or bypass a refused approval.
- Development channel: prefer an eligible native Install action when exposed. Otherwise the agent may run supported host plugin commands: register a missing source with `codex plugin marketplace add nuanu-ai/skills --ref staging`; when already registered, verify source/channel before refreshing only that source when installation needs it with `codex plugin marketplace upgrade nuanu-skills-staging`. Then `codex plugin add magicpay@nuanu-skills-staging` only if the plugin is absent. Public-directory listing is not required. Do not refresh or reinstall a correct plugin merely for a newer release. Stop on a source/channel mismatch. A successful command is not desktop activation, OAuth, or readiness. If neither permitted installation route exists, hand off **Plugins → MagicPay → Install**.
- Production channel, after promotion: select `magicpay@nuanu-skills` from the `nuanu-ai/skills` marketplace at its stable ref.
- For a real channel change, use native Disconnect for the old exact connection before native Remove for its MagicPay plugin. Keep one plugin and bundled connection; preserve unrelated integrations and remote state.
- Only when authentication is missing, start one callable native Connect action. If none is exposed, hand off **Plugins → MagicPay → Connect** and wait for it. After host-reported completion, use current-task deferred discovery and the readiness sequence in `references/setup.md`. Use one supported native reload only when actually exposed. If still blocked, report the failed phase; manual activation is not single-prompt success. Do not loop through reinstall, OAuth, new tasks, or restarts.
- Codex's plugin manager is not the retired MagicPay CLI. Use only the existing supported command in the same local host/configuration scope; do not change profiles, install/upgrade a CLI, hunt for app-bundled binaries, or launch a private App Server client. A missing or unsupported command is a host limitation. OAuth stays in native Connect: no shell-login fallback, credential handling, or alternate route after denial.

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

Release: magicpay-v0.4.20
