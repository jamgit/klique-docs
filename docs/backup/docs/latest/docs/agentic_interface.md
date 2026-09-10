# Agentic Interface

> How AI agents such as Claude Code or Cursor connect to the Klique MCP server and use its tools and skills to work with Klique.

The Agentic Interface lets AI agents such as Claude Code or Cursor work with Klique through the Klique MCP server,
using natural language instead of the Web UI or REST API directly. It runs as its own service alongside
the Klique Server, reaching the Klique API server on your behalf.

For example, rather than manually creating a project, you can simply ask:

```
Create a new project called "Image Classification Experiments".
```

The agent determines which Klique operations the request requires and performs them on your behalf, acting as a
translation layer between you and the Klique API server:

```
User → AI Agent → Klique MCP Server → Klique API Server
```

The MCP server exposes Klique's capabilities in the following ways:

* **Tools** - Individual operations an agent calls directly.
* **Resources** - Data it can read.
* **Prompts** - Ready-made instructions you invoke by name.
* **Skills** - Packaged, multi-step workflows built from those same tools that guide the agent
  through a complete task without you having to name the individual tools involved.

## Connecting to the MCP server

Klique AI apps such as Claude Code come pre-connected to the MCP server. Your own MCP client, such as
Claude Desktop or Cursor, requires a connection step.

### Klique AI apps

Klique AI apps such as [Claude Code](../webapp/applications/apps_claude_code.md),
[Codex](../webapp/applications/apps_codex.md), and [OpenCode](../webapp/applications/apps_open_code.md) come
pre-connected to the MCP server, so there is no connection step on your part.

Ask the agent to install the built-in skills; it reads them from the server and sets them up for
you. Then just describe what you want, and the agent runs the matching skill directly, for example:

```
Create a Klique app that runs my Streamlit dashboard.
```

The agent recognizes this as a job for the `create-app` skill and runs it immediately, with no connection or
installation step on your part.

### Your own agent

If you're working from your own AI agent, you need to connect the agent to the MCP server.

#### The URL to connect to

Connect your client to the **MCP server's** own `/mcp` URL, not to your Klique API server's. The MCP
server has its own host, separate from the API server. Ask your Klique administrator for its domain,
then append `/mcp`:

```
https://<your-mcp-server-domain-name>/mcp
```

The most common mistake is connecting to the API server's domain instead. That domain has no `/mcp`
route and answers with the web UI, so your client reports a connection problem rather than a wrong
address.

To verify you have the right host before configuring anything, call `GET /health` on it. The MCP
server answers `200` with a `status` of `ok` and the build it is running.

#### Getting a Bearer token

Klique authenticates MCP connections with a Bearer token issued by your Klique API server. Both
routes below produce the same thing: a token that travels to the MCP server as an `Authorization`
header. Choose one of the following options:

* **Without the ClearML SDK (recommended).** Run the `mcp-auth-headers` helper with your access and
  secret keys and it prints a ready-to-use Bearer token, performing the same OAuth exchange a standard
  MCP client would. It needs no `clearml.conf` and no SDK:
  
  ```sh
  export CLEARML_ACCESS_KEY=<your-access-key>
  export CLEARML_SECRET_KEY=<your-secret-key>
  
  uvx --from mcp-server mcp-auth-headers \
      --mcp-url https://<your-mcp-server-domain-name>/mcp \
      --format raw
  ```
  
  Pass `--format header` instead to print a JSON `Authorization` header object, ready to paste into
  an `mcp.json`. Keep the secret in the environment rather than on the command line: the helper
  accepts `--access-key` / `--secret-key` flags, but a secret typed into a command ends up in your
  shell history.
  
* **With the ClearML SDK.** If you already have valid Klique credentials in your `clearml.conf`:
  
  ```py
  from clearml import Task
  print(Task._get_default_session().token)
  ```

#### Registering the MCP server with your client

- [Claude Code](https://code.claude.com), in a shell in the project where you'll use it:
  ```sh
  claude mcp add \
      --transport http \
      klique \
      https://<your-mcp-server-domain-name>/mcp \
      --header "Authorization: Bearer <your-token-here>"
  ```
  Add `--scope user` to make the server available in every project rather than only this one.

- [Cursor](https://cursor.com/docs/mcp#installing-mcp-servers): add this to your `mcp.json`
  configuration file:
  ```json
  {
      "mcpServers": {
          "klique": {
              "url": "https://<your-mcp-server-domain-name>/mcp",
              "headers": {
                  "Authorization": "Bearer <your-token-here>"
              }
          }
      }
  }
  ```

Your client does not negotiate the token for you. Klique's MCP server advertises the OAuth
`client_credentials` grant, while MCP clients implement the interactive authorization-code flow, so
a client configured without an `Authorization` header connects and is then refused on every call.
Supplying the header is the supported path, which is what `mcp-auth-headers` exists for.

#### Testing the connection

Start your client and confirm it reports the server as connected (in Claude Code, via the `/mcp`
command).

A client reporting the server as connected has confirmed the address and the transport, not the
token: the token is only checked once a request is made. Confirm the whole path works with an
actual request, such as `List my Klique projects`.

### When calls start failing with a `401`

A connection that worked before can still start refusing every call afterward, regardless of which
client you connected. Two things to check, in order:

**Token expiration** is the most common cause. Clients store the header as a fixed string, so the
token ages out and every call begins to fail at once, which can look like the server itself
breaking. Rule this out first: mint a fresh token and update your client's configuration (in Claude
Code, `claude mcp remove klique`, then add it again).

If a fresh token does not resolve it, check the server itself. `GET /health` answering `200` means
the server is up; `GET /ready` reports whether it can reach the Klique API server that validates
your token, and answers `503` when it cannot. Neither route needs a credential.

## Working with Klique through your agent

The MCP server exposes Klique functionality as a collection of **tools**. Each tool represents an operation the
agent can perform on your behalf, covering:

* [Apps](../references/mcp_server/apps.md) - Discover the application catalog, and launch, monitor, and control
  running application instances.
* [Projects](../references/mcp_server/projects.md) - The projects that organize your tasks, models, and datasets.
* [Tasks](../references/mcp_server/tasks.md) - The units of work that run your code.
* [Queues](../references/mcp_server/queues.md) - Where tasks wait to be picked up for execution.
* [Models](../references/mcp_server/models.md) - The model registry and model-serving endpoints.
* [Users](../references/mcp_server/users.md) - Accounts, roles, invitations, and preferences in your organization.
* [Events](../references/mcp_server/events.md) - A task's console log and the scalar metrics it reported.
* [Workers](../references/mcp_server/workers.md) - The machines registered to pull and execute queued tasks.

When you make a request, the AI agent determines which tools it needs and invokes them without you having to name
them directly.

The MCP server also hosts packaged **skills**, combining several tools into a single guided workflow the agent
installs and runs by name:

| Skill | Purpose |
| --- | --- |
| `create-app` | Author your own code, script, or service into a new Klique app and bring it to a running state. |
| `monitor-app` | Check on, restart, or stop a Klique application instance that already exists. |
| `clone-app` | Make your own copy of a Klique app that already exists in the catalog, then launch it. Admin-only, and available only on server versions that support app cloning. |

See the [Skills reference](../references/mcp_server/skills.md) for installation mechanics.

For the full list of tools, resources, and prompts the MCP server exposes, module by module, see the
[MCP Server reference](../references/mcp_server/overview.md).
