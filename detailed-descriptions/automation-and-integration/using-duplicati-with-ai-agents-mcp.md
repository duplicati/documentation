---
description: >-
  Let AI agents such as Claude Code, Cursor or Codex use Duplicati as a safety
  net: run a backup before risky work, follow the task, and restore if needed,
  through the Model Context Protocol (MCP) endpoint
---

# Using Duplicati with AI agents (MCP)

The Duplicati server can expose a [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) endpoint at `/mcp`. Through this endpoint an AI agent, such as Claude Code, Claude Desktop, VS Code with GitHub Copilot, Cursor, Codex CLI, Gemini CLI or opencode, can list your backups, run a backup, follow the task, browse the backed up files and restore them.

The main use case is to let the agent take a backup of the files it is about to change, and restore them if something goes wrong. The server tells a connecting agent to work this way:

1. Call `list_backups` to find a backup whose source paths cover the files it is about to change.
2. Call `run_backup`, then `wait_for_task` until the task has finished with the result `Success` or `Warning`, and keep the time of the restore point.
3. Do the work.
4. If needed, call `restore` with that time, and wait for that task too.

The agent cannot create, change or delete backup configurations. If no backup covers the files, the agent is told to ask you to set one up.

{% hint style="warning" %}
A restore writes the backed up files back, but does not delete files that were created after the backup. It is not a full rollback of a folder. Only files below the source paths of the backup can be restored.
{% endhint %}

## Enabling the MCP endpoint

The MCP endpoint is off by default, and is currently listed as an experimental option. While it is off, requests to `/mcp` get a `404 Not Found` response, as if the server did not have the endpoint.

To turn it on in the user interface, open **Settings**, find **Experimental options**, and switch on **MCP server**. When it is on, the section **MCP access tokens** is shown below it.

The endpoint can also be turned on when starting the server:

```
duplicati-server --webservice-enable-mcp-server=true
```

The setting is stored in the [server database](../database-and-storage/the-server-database.md), so it only needs to be given once. Starting the server with `--webservice-enable-mcp-server=false` turns it off again.

{% hint style="warning" %}
Turning the MCP endpoint off deletes all access tokens that have been issued. The agents using them lose access at once, and turning the endpoint on again does not bring the tokens back.
{% endhint %}

## Access tokens

An agent connects to the MCP endpoint with an access token, sent in the request header:

```
Authorization: Bearer <token>
```

An access token only works on the MCP endpoint and cannot be used for the rest of the Duplicati API. It is limited by:

* **Permission level**, which decides which tools the agent can use:
  * **Read**: list backups, follow tasks and browse backup contents.
  * **Backup**: also run backups and stop tasks.
  * **Restore**: also restore files. This is the default.
* **Backups**: either all backups, including ones created later, or a selected list of backups. Backups outside the list are hidden from the agent, and tasks for them are shown in the queue only as "Other", without a name.
* **Restore folders** (Restore level only): a token can always restore files to the location they were backed up from. To let it restore into other places, list the folders it may restore into; it can then restore into these folders and below.
* **Lifetime**: 1 day, 30 days, 90 days (the default), 1 year or 10 years in the user interface. Any value from one minute to ten years can be given from the command line or API, as a [duration value](../../technical-details/option-formats.md#timespans-timestamps-and-durations) such as `30D` or `1Y`.

The token is shown only once, right after it is created. Duplicati does not store it and cannot show it again; if it is lost, delete it and create a new one.

A deleted token stops working at once. Tokens are also invalidated when the server password is changed, or the server is started with `--webservice-reset-jwt-config=true`, as they are signed with the same key as the other [server tokens](../../technical-details/server-authentication-model.md).

{% hint style="info" %}
Restore folders limit where the agent can ask the restore to write. They are not a security boundary against someone who can create symbolic links inside those folders, as the restore follows such links.
{% endhint %}

### Creating a token in the user interface

1. Open **Settings** and find **MCP access tokens**.
2. Click **Create access token**.
3. Enter a name, usually the agent the token is for, and choose the permission level, the lifetime and the backups the token can use. For the Restore level, you can also enter additional restore folders, one full path per line.
4. Click **Create token**.

After the token is created, choose the agent you want to use it with under **Set up the token in**. The page then shows the command or configuration for that agent, with the address of the endpoint and the token filled in, ready to copy. Click **I have copied the token** when you are done, as the token is not shown again.

The list of tokens shows the level, backups, restore folders and expiry date of each token, and has a **Delete** button to revoke it.

### Creating a token from the command line

The [ServerUtil](../../duplicati-programs/command-line-interface-cli-1/serverutil.md) can issue, list and revoke tokens:

```
duplicati-server-util issue-mcp-token --name="Claude Code" --level=Backup --backup="My documents" --expires-in=30D
```

| Option | Description |
| --- | --- |
| `--name` | Required. A name for the token, usually the agent it is for. At most 100 characters. |
| `--level` | `Read`, `Backup` or `Restore`. Defaults to `Restore`. |
| `--backup` | A backup the token can use, by ID or name. Repeat for more backups; leave out to allow all backups. |
| `--restore-root` | A folder the token can restore into, besides the original location. Repeat for more folders. Only allowed with the `Restore` level. |
| `--expires-in` | How long the token is valid, such as `30D` or `1Y`. Defaults to `90D`. |

The command prints the endpoint address and the `Authorization` header to give to the agent. To see the issued tokens and revoke one:

```
duplicati-server-util list-mcp-tokens
```

```
duplicati-server-util revoke-mcp-token <token id or name>
```

## Connecting an agent

The endpoint uses the Streamable HTTP transport of MCP, in stateless mode. Its address is the address of the Duplicati server followed by `/mcp`, for example `http://localhost:8200/mcp`. The user interface generates the setup for each agent below; the examples here use `<token>` in place of the actual token.

### Claude Code

```
claude mcp add --transport http duplicati http://localhost:8200/mcp --header "Authorization: Bearer <token>"
```

### VS Code (GitHub Copilot)

Add this to `.vscode/mcp.json` in the workspace, or run **MCP: Open User Configuration** and add it there:

```json
{
  "servers": {
    "duplicati": {
      "type": "http",
      "url": "http://localhost:8200/mcp",
      "headers": { "Authorization": "Bearer <token>" }
    }
  }
}
```

### Cursor

Add this to `~/.cursor/mcp.json`, or to `.cursor/mcp.json` in the project:

```json
{
  "mcpServers": {
    "duplicati": {
      "url": "http://localhost:8200/mcp",
      "headers": { "Authorization": "Bearer <token>" }
    }
  }
}
```

### Codex CLI

Add this to `~/.codex/config.toml`:

```toml
[mcp_servers.duplicati]
url = "http://localhost:8200/mcp"
http_headers = { "Authorization" = "Bearer <token>" }
```

### Gemini CLI

Add this to `~/.gemini/settings.json`, or to `.gemini/settings.json` in the project:

```json
{
  "mcpServers": {
    "duplicati": {
      "httpUrl": "http://localhost:8200/mcp",
      "headers": { "Authorization": "Bearer <token>" }
    }
  }
}
```

### GitHub Copilot CLI

Add this to `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "duplicati": {
      "type": "http",
      "url": "http://localhost:8200/mcp",
      "headers": { "Authorization": "Bearer <token>" },
      "tools": ["*"]
    }
  }
}
```

### opencode

Add this to `opencode.jsonc`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "duplicati": {
        "type": "remote",
        "url": "http://localhost:8200/mcp",
        "oauth": false,
        "headers": { "Authorization": "Bearer <token>" }
      }
    }
  }
}
```

### Claude Desktop and other agents that start local commands

Some agents, such as Claude Desktop, cannot connect to a local HTTP server directly and instead start an MCP server as a local command that talks over standard input and output. For these agents, the ServerUtil has an `mcp` command that acts as a bridge and forwards everything to the `/mcp` endpoint.

For Claude Desktop, open **Settings**, then **Developer**, choose **Edit Config**, and add this to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "duplicati": {
      "command": "duplicati-server-util",
      "args": ["mcp", "--hosturl", "http://localhost:8200"],
      "env": { "DUPLICATI_MCP_TOKEN": "<token>" }
    }
  }
}
```

On Windows, the command is `Duplicati.CommandLine.ServerUtil.exe`. If the agent cannot find the command, enter its full path.

The bridge reads the token from the environment variable `DUPLICATI_MCP_TOKEN`, or from a file given with `--token-file=<path>`. If no token is given, the bridge logs in to the server the same way as the [other ServerUtil commands](../../duplicati-programs/command-line-interface-cli-1/serverutil.md#handling-login), which gives the agent **full access** without the limits of a token. Using a token is recommended.

### Other agents

Any agent that supports remote MCP servers with the Streamable HTTP transport can connect, using the endpoint address and the `Authorization: Bearer <token>` header.

## Agent skill

The instructions the server gives a connecting agent are short. For agents that support [Agent Skills](https://agentskills.io), Duplicati also has a skill, `duplicati-backup`, that describes the whole workflow in more detail: when to take a checkpoint, how to record the restore point, how to restore only the files that need it, and what to ask you before overwriting files. The skill also tells the agent how to guide you through the setup on this page if the Duplicati tools are not connected.

The skill is in the [duplicati/agent-skills](https://github.com/duplicati/agent-skills) repository. The skill does not replace the MCP setup: the agent still needs the endpoint and a token as described above.

### OpenClaw

The skill is published on [ClawHub](https://clawhub.ai):

```
clawhub install @duplicati/duplicati-backup
```

### Hermes

```
hermes skills install duplicati/agent-skills/skills/duplicati-backup
```

### Claude Code, Codex, Cursor and other agents

Install it with the [skills](https://skills.sh) command line tool:

```
npx skills add duplicati/agent-skills
```

Or copy the `skills/duplicati-backup` folder from the repository into the skills folder of the agent, for example `~/.claude/skills/` for Claude Code.

## Available tools

| Tool | Level | Description |
| --- | --- | --- |
| `get_server_status` | Read | Reports the server version, whether the server is paused, and the running and waiting tasks. |
| `list_backups` | Read | Lists the backups the token can use, with ID, name, source paths and last run. |
| `list_versions` | Read | Lists the versions (points in time) of a backup, newest first. |
| `list_files` | Read | Lists the files and folders in one folder of a backup version, one page at a time. |
| `search_files` | Read | Finds files and folders in a backup version whose path matches a filter with `*` and `?` wildcards. |
| `get_task` | Read | Reports the state of a task without waiting: Waiting, Running, Completed or Failed. |
| `wait_for_task` | Read | Waits for a task to finish, for at most 300 seconds per call (45 by default), and reports its state and progress. |
| `run_backup` | Backup | Queues a run of a backup and returns the task ID at once. The finished task reports the restore point. |
| `stop_task` | Backup | Stops a running task, either cleanly after the current file or as fast as possible. A stopped backup keeps what it has uploaded as a partial version. |
| `restore` | Restore | Queues a restore of all or selected files from a backup version, to the original location or to another folder, with or without overwriting existing files. |

Backups are given by ID or exact name, and versions by time, as version numbers shift each time a backup runs. A restore through MCP can only write to a local folder, not to a remote destination.

Tasks run one at a time, in queue order, and a backup or restore can take from seconds to hours. Task IDs are forgotten when the server restarts. If the server is paused, a waiting task does not start until the server is resumed, which `wait_for_task` reports to the agent.

When a tool is refused, for example because the token has a lower level or the backup is not in its list, the agent gets a message saying why, so it can ask you for a token with more access.

## Security considerations

* The MCP endpoint applies the same checks as the rest of the server to protect against requests from web pages: the host name must be one of the [allowed host names](../../duplicati-programs/server.md#external-access-to-the-server), a request from a browser must come from an allowed origin, and the pre-shared key must be present if one is configured. Requests that fail these checks get `403 Forbidden`.
* The endpoint also accepts the regular credentials of the Duplicati API. A caller using these, including the ServerUtil bridge without a token, has full access to all backups and all tools.
* Give each agent its own token, with the lowest level, the fewest backups and the shortest lifetime that fits the work. A `Read` or `Backup` token cannot overwrite your files.
* The `restore` tool can overwrite existing files when the agent asks for it. Only give the `Restore` level to agents you trust to do so.
* Anyone who has the token can use it until it expires or is deleted. Treat it like a password, and avoid committing agent configuration files that contain it to a shared repository.
