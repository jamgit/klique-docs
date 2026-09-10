# Remote Agentic Development Environment

> Klique's browser-based remote development environment, pairing VS Code with agentic coding CLIs like Claude Code and Codex on managed compute.

The Remote Agentic Development Environment gives users a governed, browser-based workspace with an agentic coding
assistant built in. It runs on the compute the organization already manages, so builders get the full power of an
agentic coding tool without installing anything locally, managing their own API keys, or provisioning infrastructure.

Each environment pairs a browser-based VS Code with an agentic coding CLI that is ready to use the moment the session
opens. Traffic to the model provider passes through Klique, so token usage is metered and attributed rather than
happening off-platform.

For Coding agents such as Claude, Klique offers a host of preconfigured skills for coding, as well as building
and distributing organizational applications using Klique's applications.

## What's available

Klique provides a remote agentic development environment for each of the following coding CLIs:

* [Claude Code](../webapp/applications/apps_claude_code.md) - A detachable remote VS Code session pre-configured with
  Anthropic's Claude Code CLI.
* [Codex](../webapp/applications/apps_codex.md) - A detachable remote VS Code session pre-configured with OpenAI's
  Codex CLI.
* [OpenCode](../webapp/applications/apps_open_code.md) - A detachable remote VS Code session pre-configured with
  OpenCode's CLI.

## How it fits

The Remote Agentic Development Environment is one of Klique's core [AI Engine](ai_engine.md) capabilities. It runs on compute
managed by the [Orchestration Engine](../orchestration_engine/orchestration_engine.md), and its identity, access, and
token usage are governed by the [Governance Engine](../governance_engine/governance_engine.md). An application a user
builds inside a session can be onboarded as an
[Agentic Generated Application](agentic_generated_applications.md) and published for others to launch.

## Capabilities

- **Governed by default**: sessions run under the user's own identity and the permissions their role carries, on
  approved models, inside the boundaries administrators set, without depending on personal API keys.
- **Cost visibility**: every model call is metered, so token usage and cost are attributed per user and feed the
  platform's [usage metering and billing](../governance_engine/usage_metering_billing.md).
- **Runs on managed compute**: the environment draws from the same governed compute as the rest of the platform,
  giving access to GPUs and organizational resources with no local setup.
- **Ready immediately**: the coding assistant is pre-installed and pre-configured, so a session opens straight into a
  working terminal.
- **Centralized model access**: sessions can route through the [Klique LLM Gateway](../governance_engine/tokenomics.md),
  so model choice and cost stay centrally governed and access adapts as the available models change.
- **Persistent workspaces**: work carries across sessions, so a stopped environment can be resumed rather than rebuilt.
- **Extensible**: sessions can be tailored with additional tooling and skills, including the ability to build and
  publish new Klique applications from inside the environment.
