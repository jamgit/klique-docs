# Agentic Desktop Applications

> Klique's managed desktop environments for agentic tools such as Claude Desktop and ChatGPT, run centrally on governed compute.

Klique's Agentic Desktop Applications give an organization a managed desktop environment for the agentic tools people
already want to use, such as Claude Desktop and ChatGPT. Instead, users downloading and running these applications
on their own machine, the organization provisions them centrally: they run on the organization's resources, 
are reached remotely, using approved internal and external model endpoints with usage monitored for internal billing.

## What's available

Klique provides managed desktop environments for common agentic desktop applications (for example [Claude Desktop](../webapp/applications/apps_claude_desktop.md)).

## How it fits

Agentic Desktop Applications are one of Klique's [AI Engine](ai_engine.md) capabilities. They run on compute managed by
the [Orchestration Engine](../orchestration_engine/orchestration_engine.md), and their identity, access, and usage are
governed by the [Governance Engine](../governance_engine/governance_engine.md).

## Capabilities

- **Runs on the organization's resources**: applications run centrally on managed compute rather than on personal
  machines, so users do not download or install anything locally.
- **Centrally managed and updated**: administrators provision, configure, and update the applications in one place, so
  every user runs a consistent, approved version.
- **Sandboxed**: each application runs in an isolated, sandboxed environment, keeping it contained from the host and
  from other workloads.
- **Governed access**: applications run under the user's own identity and the permissions their role carries, inside the
  boundaries administrators set.
- **Consistent with the rest of the platform**: like other Klique applications, desktop environments draw from the
  managed compute and are reached through Klique rather than set up by each user.
