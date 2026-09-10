# Claude Code

> The Claude Code application, which launches a remote VS Code session pre-configured with the Claude Code CLI.

The Claude Code app launches a detachable remote VS Code session pre-configured with the Claude Code CLI, ready to
use as soon as the session starts.

The Claude Code app offers workspace management features, allowing you to store, sync, and restore interactive
workspaces across sessions. This ensures your work, including `~/.claude` session state, is preserved and can be
easily accessed in future sessions.

The Claude Code session is set up using a [Klique Orchestrator](../../orchestrator.md). When configuring an app instance,
select a queue, and the Klique Orchestrator servicing that queue will set up the remote environment. When setup is complete, the
app instance dashboard displays the session ID and a link to open the VS Code session with Claude Code ready to use.

Token usage for the session is metered by an outbound proxy and reported back to the parent task, so consumption is
visible in the dashboard's token utilization charts regardless of which AI provider is used. Routing calls through
the [LLM Gateway](../settings/webapp_settings_llm_gateway.md) (see **Vendor Endpoint** below) additionally surfaces
the session's usage in the [AI Usage Dashboard](../webapp_admin_dashboard_ai_usage.md) and lets administrators
enforce token usage quotas on it.

The application monitors the session's activity and shuts down if it is inactive for a specified maximum idle time.

Once you start a Claude Code instance, you can view the following information in its dashboard:

* App status indicator
  *  - Claude Code session is active
  *  - Claude Code session is setting up
  *  - Claude Code session is idle
  *  - Claude Code session is stopped
* Restored workspace - If a previous session's workspace was restored, this will display that session's ID
* Current session ID
* Open Claude - Link to the VS Code session with Claude Code ready to use
* Token utilization over time:
  * Tokens (input)
  * Tokens (output)
  * Tokens cumulative (input)
  * Tokens cumulative (output)
  * Tokens cumulative (total)
* Resource utilization over time:
  * CPU Utilization
  * GPU Utilization
* Console - The console log shows the instance's activity, including setup progress, status changes, and error messages

:::info[Klique MCP Server]
This session comes pre-connected to the [Klique MCP server](../../agentic_interface/agentic_interface.md), with its tools and built-in skills
already available. Ask Claude Code to work with your Klique projects, tasks, models, and apps directly, with no
manual connection or installation step.
:::

:::tip[Embedding Visualizations]
You can embed plots from the app instance dashboard into [Reports](../webapp_reports.md). Klique 
also supports embedding resources in third-party platforms that support embedded content (e.g. Notion). These visualizations 
are updated live as the app instance(s) updates. Hover over the plot and click  
to copy the embed code, and navigate to a report to paste the embed code.
:::

## Claude Code Instance Configuration
When configuring a new Claude Code instance, you can fill in the required parameters or reuse the configuration of 
a previously launched instance.  

Launch an app instance with the configuration of a previously launched instance using one of the following options:
* Cloning a previously launched app instance will open the instance launch form with the original instance's 
configuration prefilled.
* Importing an app configuration file. You can export the configuration of a previously launched instance as a JSON file 
when viewing its configuration.

The prefilled instance launch form can be edited before starting the new app instance. 

To configure a new app instance, click `Launch New`  
to open the app's instance launch form.

### Configuration Options

:::note
Administrators can [customize](../../deploying_klique/app_launch_form_custom.md) the launch form and 
modify field names and/or available options and defaults. 

This section describes the default configuration provided by Klique.
:::

* **Compute Resource (Queue)** – The [Queue](https://clear.ml/docs/latest/docs/fundamentals/agents_and_queues/#what-is-a-queue) serviced by the 
  Klique Orchestrator that will run the Claude Code session.
* **Claude Code Session Project** - The project where the app instance is created. Access follows project-level 
  permissions: users with read access to the project can use the app.
* **AI Provider**
  * Set a Custom Anthropic API Key - Select to provide a custom Anthropic API key for the session instead of the default key
    from your [User Vault](../settings/webapp_settings_profile.md#configuration-vault).
  * Vendor Endpoint - Route model calls through the [LLM Gateway](../settings/webapp_settings_llm_gateway.md) instead 
    of calling Anthropic's API directly. Leave blank to call the API directly. Sets 
    `SESSION_PROXY_UPSTREAM` and `ANTHROPIC_MODEL` in the container.
  * Update Claude Code at Session Start - When enabled, updates the Claude Code CLI to the latest version when the 
    session starts. When disabled, the session pins to the image-baked version.
* **Skill packs** - Preconfigured skill packs available to enable for the session:
  * [ECC](https://github.com/affaan-m/ecc) - Enforces security and coding standards.
  * [impeccable](https://github.com/pbakaus/impeccable) - Enforces design quality rules.
  * [caveman](https://github.com/juliusbrussee/caveman) - Switches Claude's responses to a terse mode.
  * [gstack](https://github.com/garrytan/gstack) - Enables browser automation.
* **Advanced Options**
  * Session Name - Name for the app instance. This will appear in the instance list
  * Continue from Previous Session - Select the ID of a previous Claude Code session to restore
  * Container Language - Base runtime image for the session container. Every option includes both Python and Node.js 
    at their latest versions; select `Custom` to provide your own image from a registry instead.
  * Environment Variables - One `KEY=value` per line. Injected into every shell inside the session via 
    `/etc/profile.d/claude_code.sh`. Lines starting with `#` are ignored.
  * Workspace Autosave - Periodically snapshot the workspace while the session runs, so a crash or hard stop loses at 
    most one interval. Uncheck to only snapshot on graceful shutdown.
  * Persistent Workspace Path - Directory snapshotted when the session closes and loaded back in when a new 
    session is started with `Continue from Previous Session` pointing at it. Defaults to `~/`, so dotfiles, 
    `~/.claude` state, and cloned repos all persist across sessions. Leave blank for an ephemeral session.
  * Idle Shutdown (Hours) - Maximum idle time after which the app instance will shut down
  * Idle CPU Threshold (%) - CPU utilization under which the session is considered idle
  * Extra Python Packages - Additional Python packages to install at session start
