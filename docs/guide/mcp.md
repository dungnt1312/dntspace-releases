# MCP servers

dntspace supports **MCP over stdio (v1)**: JSON-RPC 2.0 with **Content-Length** framing on stdin/stdout.

Configure under **Settings → Profile → MCP**.

## Add a server

1. Open **Settings → MCP → Add**.
2. Set:
   - **id** — short id, **no `__`** (used in tool names)
   - **name** — display label
   - **command** + **args** — stdio process (e.g. `npx` + `-y @modelcontextprotocol/server-filesystem <path>`)
3. Enable and **Save**.
4. dntspace spawns the process, runs `initialize` + `tools/list`, and registers tools as:

```text
mcp__{serverId}__{toolName}
```

5. Use **Reconnect** after fixing a failed server. Connection errors show under each row.

## Mode policy for MCP tools

| Mode | MCP tools |
|------|-----------|
| Ask before edit | **Needs approval** |
| Plan | **Hidden** |
| Edit automation | **Needs approval** |
| Full access | **Auto** |

MCP needs approval in **Ask before edit** and **Edit automation**. Auto only in **Full access**. Prefer Ask (or Edit automation) with untrusted MCP servers.

## Timeouts & protocol

- Connect timeout: ~30s  
- `tools/call` timeout: **60s**  
- Transport: stdio only (no SSE/HTTP MCP in this version)

## Multi-profile caveat (important)

- MCP **config JSON is stored per profile**.
- The **live** connection / tool registry is still **process-global**.
- Saving or reconnecting MCP from one open profile window **replaces** the in-process registry for all windows.

Until per-runtime isolation ships, treat MCP as “last writer wins” across multi-window sessions.

## Pack MCP

Enabled packs can contribute MCP servers declared in the pack manifest (e.g. content-publisher’s Node `publisher` server). Those tools use pack-namespaced ids such as:

```text
mcp__pack_content_publisher_publisher__fetch_url
```

Pack enable may **fail closed** if declared MCP cannot connect (e.g. `node` missing on `PATH`). See [Packs & workflows](./packs-and-workflows.md).

## Security notes

- MCP servers can run arbitrary local processes — only add servers you trust.
- Approval mode is the main UX control; it is not a full sandbox.
- Do not point filesystem MCP servers at paths outside what you intend the agent to touch.

## Related

- [Tools & approvals](./tools-and-approvals.md)  
- [Profiles & workspaces](./profiles.md)  
