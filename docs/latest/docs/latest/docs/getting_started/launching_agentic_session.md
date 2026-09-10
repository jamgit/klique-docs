# Launching an Agentic Coding Session

> Launching an agentic coding CLI session on managed compute, working with the MCP server, and returning to a previous session.

You can launch a fully configured agent CLI in a browser-based VS Code running on managed compute, with no
local install and no API keys to manage, using the following Klique apps:

* [Claude Code](../webapp/applications/apps_claude_code.md) - Anthropic's Claude Code CLI, with optional skill
  packs (ECC, impeccable, caveman, gstack) enabled at launch.
* [Codex](../webapp/applications/apps_codex.md) - OpenAI's Codex CLI.
* [OpenCode](../webapp/applications/apps_open_code.md) - OpenCode's CLI.

For the underlying capability, see
[Remote Agentic Development Environment](../ai_engine/remote_agentic_development_environment.md).

## Launch a Session
Each app instance is set up using a [Klique Orchestrator](../orchestrator.md): select a queue and project, and
the orchestrator servicing that queue builds the remote environment. When launching an app instance, also configure:

* **AI Provider** - Every session's token usage is metered by an outbound proxy and displayed in the app instance's
  dashboard. Routing calls through the [LLM Gateway](../whats_klique/llm_gateway.md) (the
  Vendor Endpoint option) instead of calling the provider directly also surfaces that usage in the [AI
  Usage dashboard](../webapp/webapp_admin_dashboard_ai_usage.md) and lets administrators enforce 
  [token quotas](token_quota_control.md) on that usage; a custom API key bypasses the gateway.
* **Container Image** - The base runtime image for the session container,
  or a custom image from a registry.
* **Environment Variables** - Injected into every shell inside the session at start.

## Work in the Session

Every session comes connected to the [Klique MCP server](../agentic_interface/agentic_interface.md), so the
agent can work with your Klique projects, tasks, models, and apps directly, with no manual connection or
installation step.

The instance dashboard tracks token utilization (input, output, and cumulative) and resource utilization (CPU,
GPU) for the session, alongside the console log of its setup and activity.

## Return to a Session

After a session shuts down, you can launch another one that returns to its previous state instead of starting from a 
blank environment. That new instance doesn't resume the old session: it's a fresh container loaded with the old one's 
files.

That loading works through the **Persistent Workspace Path**: a directory snapshotted when a session closes and
restored into a new session pointed at the same path. It defaults to `~/`, so dotfiles, the CLI's state directory
(`~/.claude`, `~/.codex`, or `~/.config/opencode`), and any cloned repos all persist across sessions. Leave it
blank for an ephemeral session, with nothing to restore. Since the field is set by default, restoring is the
common case rather than something you need to opt into.

Point a new session at a previous one's workspace in of the following ways:

* When configuring a new instance, select the previous session's ID under **Continue from Previous Session** 
* **Clone** the instance using its context menu, which opens a new launch form prefilled with its configuration.
* **Relaunch** the instance using its context menu, which stops the running instance and launches a fresh one with its
  configuration directly, removing the original once the new instance reaches a terminal status.

All of these options restore the workspace at its persistent path.
