# Install MagicPay for Codex

Download the release archive and place the magicpay folder in a supported skill directory.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- First discover current-task MagicPay tools, including deferred tools. When `get_magicpay_capabilities` is callable, verify the requested environment and authenticated readiness without requiring separate inventory. An auth challenge goes to native Connect; a service failure is not proof of absence. Otherwise inspect native inventory or the existing host's `codex plugin list --json` and `codex plugin marketplace list --json` when permitted. Preserve a correct installation and authorization. Missing inventory access does not erase a confirmed installation or completed OAuth; reuse that evidence for catalog recovery. Only if neither inventory route is available and state has not otherwise been established, state is unknown: hand off **Plugins → MagicPay** to inspect details; Install only if absence is confirmed; do not invent a Connect button. Missing tools do not prove absence. Never automate Codex itself or bypass a refused approval.
- Development channel: prefer an eligible native Install action when exposed. Otherwise the agent may run supported host plugin commands: register a missing source with `codex plugin marketplace add nuanu-ai/skills --ref staging`; when already registered, verify source/channel before refreshing only that source when installation needs it with `codex plugin marketplace upgrade nuanu-skills-staging`. Then `codex plugin add magicpay@nuanu-skills-staging` only if the plugin is absent. Public-directory listing is not required. Do not refresh or reinstall a correct plugin merely for a newer release. Stop on a source/channel mismatch. A successful command is not desktop activation, OAuth, or readiness. If neither permitted installation route exists, hand off **Plugins → MagicPay → Install**.
- Production channel, after promotion: select `magicpay@nuanu-skills` from the `nuanu-ai/skills` marketplace at its stable ref.
- For a real channel change, use native Disconnect for the old exact connection before native Remove for its MagicPay plugin. Keep one plugin and bundled connection; preserve unrelated integrations and remote state.
- Primary path: call `get_magicpay_capabilities` once through the current task tool channel so its `mcp/www_authenticate` result can display the native MCP connection prompt. This does not need a separate Connect tool. Let the user approve the prompt and sign in securely; do not start another flow while it is pending. Only if tools cannot load or no prompt appears, try one callable native Connect action. Otherwise, when the exact installed connection requires sign-in, the agent checks `codex --version` and runs `codex mcp login magicpay` once. Require Codex 0.147.0 or newer; 0.146.1 drops the callback issuer. An older, unknown, or unsupported command is a host limitation. Missing tools alone do not establish that sign-in is required. Let Codex open the secure browser; if handoff is needed, use its exact authorization URL with the host browser. Keep OAuth URLs out of transcript output; never reconstruct callbacks, relay parameters, or handle credentials. The user runs no commands and does not visit Plugins to start this flow. If neither supported connection action is available, report the observed limit without inventing a Connect button. An open sign-in screen is not completed authentication. Never recreate the tool call in a shell client to trigger UI. After host-reported completion, use current-task deferred discovery and the readiness sequence in `references/setup.md`. Use one supported native reload only when actually exposed. If tools remain unavailable after confirmed installation and OAuth, with no separate auth/service error, give one new-chat handoff. Tell the user MagicPay is installed and signed in, then ask them to open a new Codex chat and send: "Verify my MagicPay development connection. I already installed and signed in, then opened this new chat because the tools were missing." Adapt the name and prompt to the requested environment. Keep the existing installation and sign-in; no setup steps need repeating. Keep that recovery cue: if this is already the new chat and tools remain unavailable, report tool loading blocked without another chat, reinstall, restart, or sign-in. Unknown installation/auth state or pending, canceled, denied, or failed OAuth does not qualify. Handle actual auth/service errors separately. If tools are callable, finish readiness here instead. The new chat reuses authorization and verifies capabilities, authenticated status, and balance. This is activation recovery, not single-prompt success; conversation history and payment approvals do not carry over. See `references/runtime-setup.md` for handoff details.
- Codex's plugin manager is not the retired MagicPay CLI. Use only the existing supported command in the same local host/configuration scope; do not change profiles, install/upgrade a CLI, hunt for app-bundled binaries, or launch a private App Server client. A missing or unsupported command is a host limitation. OAuth stays host-managed through the native prompt or supported Codex command; no credential handling or alternate route after denial. The retired MagicPay CLI remains abandoned.

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

Release: magicpay-v0.4.24
