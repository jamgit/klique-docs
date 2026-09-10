# MCP Server

> Klique MCP server modules for apps, events, models, projects, queues, tasks, users, and workers, plus hosted skills, as tools, resources, and prompts.

The Klique MCP server exposes Klique platform capabilities by allowing an AI agent to discover
functionalities of the Klique API server via descriptions of them in text format,
following the [Model Context Protocol](https://modelcontextprotocol.io).

Each set of functionalities below acts under the identity of the authenticated caller and reaches
the Klique API server, requiring a valid Klique Bearer token header, except where noted otherwise.

The server also exposes two routes outside these modules that need no credential: `/health` and
`/ready`. Both let you check the server is up and can reach its API server before you have a token.

For a walkthrough of connecting an agent and working with these modules together, see
[Agentic Interface](../../agentic_interface/agentic_interface.md).

## Modules

* [Apps](apps.md): discover, install, and manage the application catalog, and launch, monitor, and manage running
  application instances.
* [Events](events.md): read and export a task's console log (`stdout`/`stderr`), and read the scalar metrics a
  task reported.
* [Models](models.md): search, register, edit, and delete model registry records, and inspect model serving endpoints
  and their container instances.
* [Projects](projects.md): list, create, update, and delete the projects that organize tasks, models, and datasets,
  and inspect the tags, contributors, metrics, and hyper-parameters in use across one.
* [Queues](queues.md): manage execution queues and move tasks between them.
* [Tasks](tasks.md): list, create, clone, update, stop, archive, and delete tasks, the units of work that run your
  code.
* [Users](users.md): manage the members of your Klique organization, their roles and preferences, the addresses
  invited to join it, and the accounts removed from it.
* [Workers](workers.md): list the worker machines registered to pull and execute queued tasks.

## Skills

* [Skills](skills.md): browse and download ready-made agent skills that combine tools from the modules above into
  complete, guided workflows.

## Checking the connection

Alongside the modules above, the server exposes a `ping` tool, a `ping` prompt, and a `klique://ping`
resource. All three answer immediately, touch nothing, and take no arguments. Use one to confirm your
agent really is talking to the server before you conclude that some other tool is broken.
