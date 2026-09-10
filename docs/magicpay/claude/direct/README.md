# Install MagicPay for Claude Code

Use the host plugin below. The direct skill archive is an optional alternative.

## Host plugin (recommended)

The host plugin bundles this skill and the remote MCP declaration, so no archive download is needed.

- First discover current-task MagicPay tools, including deferred tools. When `get_magicpay_capabilities` is callable, verify the requested environment and authenticated readiness without requiring separate inventory. An auth challenge goes to Connect; a service failure is not proof of absence. Otherwise inspect `claude plugin list --json` and `claude plugin marketplace list --json` in the same host and configuration scope. Preserve an already-correct installation and authorization, including a supported older version. If inventory is unavailable, state is unknown: hand off `/plugin` to inspect details; Install only if absent, Authenticate only when needed. Report existing duplicates; do not remove them automatically or create another connection. Use existing supported host commands; if unavailable, hand off the native control instead of installing another CLI. A denied host action stops the attempt; do not switch routes to bypass it.
- Development channel: When development installation is needed, verify any existing `nuanu-skills-staging` source is `nuanu-ai/skills` at ref `staging`; stop on a source/channel mismatch. Register only a missing source with `claude plugin marketplace add https://github.com/nuanu-ai/skills.git#staging` (skip when already registered), then install only the absent plugin with `claude plugin install magicpay@nuanu-skills-staging`. Public-directory listing is not required.
- Production channel, after promotion: When production installation is needed after promotion, verify any existing `nuanu-skills` source is the stable `nuanu-ai/skills` marketplace; stop on a source/channel mismatch. Register only a missing source with `claude plugin marketplace add nuanu-ai/skills`, then install only the absent plugin with `claude plugin install magicpay@nuanu-skills`.
- Check the in-session install summary: if active, continue; after a shell install or an activation-required summary, the user enters `/reload-plugins` in this same conversation before `/mcp` can authenticate the new server. If reload stops with a prompt-cache warning, explain the cost of re-reading the conversation; the user may accept it with `/reload-plugins --force`. Install success is not authentication or same-session readiness.
- The connection is `plugin:magicpay:magicpay`. This host has no model-triggered sign-in: no tool, hook, or agent shell can open the MagicPay window, and `claude mcp login plugin:magicpay:magicpay` needs a real interactive terminal, so do not create a PTY workaround and do not send the user to a terminal. After any required install activation, reuse authorization. When sign-in is needed, give the user exactly one action and nothing else: run `/mcp`, select `magicpay`, choose Authenticate, and enter email and code only in the secure MagicPay window that opens from there; say you will verify readiness right after. Manual handoff: **/mcp → magicpay → Authenticate**. Catalog refresh: sign-in needs no reload, because the tools are available on the next request; after completed OAuth, discover `get_magicpay_capabilities` in this session first. Only if tools remain unavailable after host-reported authorization, the user runs `/reload-plugins` in this same conversation, following the cache-warning rule under Install, then retry discovery. This is a user command, not a shell or model reload API. Reconnect only for an observed connection failure. Do not launch a separate `claude -p` probe or treat its account login as the plugin OAuth result; report the remaining phase if recovery fails. A user reload is same-chat recovery, not automatic single-prompt completion.

## Direct skill archive

1. Install the MagicPay skill.
2. Connect the remote MagicPay MCP for this runtime.
3. Follow [Connection Setup](../../references/setup.md) to verify the selected environment, capabilities, authenticated status, and balance. Installation alone is not readiness.

Supported skill directories:

- .claude/skills/
- ~/.claude/skills/

## Browser architecture

The runtime built-in browser is the only page-control owner. It navigates,
analyzes, fills ordinary fields, chooses exact protected targets, and
interprets results, and owns the one final commitment. Do not install or
start a second browser controller.

Release: magicpay-v0.4.43
