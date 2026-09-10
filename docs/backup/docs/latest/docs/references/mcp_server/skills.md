# Skills

> The Klique MCP server's skills module, with the built-in agent skills it hosts, the klique://skills resource listing them, and how to extend them.

The Klique MCP server exposes functionalities to work with skills: browsing and downloading the
ready-made agent skills it hosts, packaged as `.zip` files retrievable via GET request.

## Built-in skills

The server ships three skills covering the Klique application lifecycle, from creating an app to keeping it healthy:

### create-app

Author your own code, script, or service into a new tenant-owned Klique app and bring it to a launchable, running state.

Use it when you bring software you want packaged as an app ("create an app that runs X", "package this script as an app", "publish my Streamlit dashboard as an app").

It covers:

- Authoring the `.app.conf` plus the code payload
- Installing with the `upload_app` MCP tool
- Launching with the `launch_app_instance` MCP tool
- Confirming the instance runs

To copy an app already in the catalog use `clone-app` instead; to check on an existing app use `monitor-app`.

### monitor-app

Monitor and manage a Klique application instance that already exists and is assumed to be running.

Use it when you ask about a running app ("is my app up?", "give me the app's URL", "why did my app die?"), want to change or restart it, or want to stop or remove it.

It covers:

- Finding instances (the `list_app_instances` MCP tool)
- Reading an instance's status and dashboard URL
- Diagnosing failures from the task log
- The full lifecycle (edit / relaunch / stop / delete)

It is also the tail end of `create-app`: confirming a fresh launch came up.

### clone-app

Make your own tenant-owned copy of a Klique app that already exists in the catalog, then launch it and confirm it runs.

Use it when you want "your own copy" of a catalog app ("clone the JupyterLab app for my team", "duplicate that app"). A single MCP tool call creates the copy: no `.app.conf`, no code payload, no artifacts. Admin-only, and available only on server versions that support app cloning.

Not for authoring brand-new software into an app (that is `create-app`) or for managing an app that already exists (that is `monitor-app`).

## Downloading and installing skills

Each skill in the catalog is served at:

```
GET <mcp-server-public-base-url>/skills/<skill-name>
```

An MCP-connected agent that reads the `klique://skills` resource receives instructions on how to install the skills on your machine and can download and install any skill available there (or all of them) for you.

The route requires an `Authorization: Bearer` header carrying a valid Klique token. The authentication process follows the [Client Credentials Flow](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) (OAuth 2.0) for machine-used credentials, so that your agent connected to the MCP server can use the same Bearer token for the MCP connection and to download the skills via GET requests.

The response of the GET request is a `.zip` archive (`application/zip`, served as an attachment named `<skill-name>.zip`) whose entries are rooted at `<skill-name>/`, so extracting it into your MCP client's skills folder yields a ready-to-use skill directory the client discovers automatically. Here's an example command with the `create-app` skill:

```sh
curl -fsSL -H "Authorization: Bearer $CLEARML_AUTH_TOKEN" \
  <public-base-url>/skills/create-app \
  -o /tmp/create-app.zip \
  && unzip \
  -o /tmp/create-app.zip \
  -d <skills-folder>
```

Replace `<skills-folder>` with your client's skills directory (e.g. for Claude Code, that is `.claude/skills/`, giving `.claude/skills/create-app/SKILL.md` after extraction) and supply a valid Klique Bearer token as `$CLEARML_AUTH_TOKEN`.

Skills are versioned, so you can tell whether the copy on your machine is current: compare the `version` in the installed skill's `SKILL.md` frontmatter with the `version` the `klique://skills` listing reports. If they differ, re-run the download to replace it.

## Custom skills

You are welcome to extend the functionality of the Klique MCP server with your own custom skills. As long as you follow the conventions from your MCP client (most of them, such as Claude Code, Codex, Cursor, etc. support [agentskills.io](https://agentskills.io) conventions), your MCP client will figure out how to use your skill together with the Klique MCP.

For example, with Claude Code, all you'd have to do is create a directory under `.claude/skills/my-custom-skill` that contains a `SKILL.md` file with instructions, following [agentskills.io](https://agentskills.io) conventions for its contents. As soon as the file exists, without even restarting your Claude Code session, the command `/my-custom-skill` will be available to you and interact with your Klique MCP server (assuming it is connected).

## Resources

### klique://skills

The catalog of every skill the server offers for installation, with everything an agent needs to install one: what each skill is for, where to download it, and the shell commands that perform the download and installation. Read it whenever you want to know what skills this server provides, or when the user asks to install one (or all) of them.

The listing is computed live at read time: the server enumerates its skills assets folder on every read, so the catalog always reflects the archives currently present, including custom skills added while the server is running (see **Custom skills** below). Discovery and download can therefore never disagree.

**Parameters**

None.

**Returns**

A JSON document with one entry per available skill, each carrying:

| Field | Description |
| --- | --- |
| `name` | The skill's name, also the final path segment of its download URL. |
| `description` | A short description (under 15 words) of what the skill does. |
| `version` | The skill's version. An installed copy whose own version differs from the one listed here is stale and should be reinstalled. |
| `download_url` | The absolute URL the skill's zip archive is served at, built from the server's public base URL plus `/skills/<name>`. |
| `install_instructions` | A shell snippet that downloads the archive and extracts it into the client's skills folder, e.g. `curl -fsSL -H "Authorization: Bearer $CLEARML_AUTH_TOKEN" <download-url> -o /tmp/<name>.zip && unzip -o /tmp/<name>.zip -d <skills-folder>`. |

The document also carries a top-level `note` explaining the two placeholders in the install snippets: `$CLEARML_AUTH_TOKEN` must be a valid Klique bearer token supplied by the caller, and `<skills-folder>` is the MCP client's own skills directory (for example `.claude/skills/` for Claude Code).
