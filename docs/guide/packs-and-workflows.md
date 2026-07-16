# Packs & workflows

Packs extend dntspace with **skills**, **MCP servers**, **workflows**, and optional **config/secrets**.

Manage under **Settings → Packs**.

## Import & lifecycle

1. **Import** a `.dntpack` / zip or a **folder** that contains `manifest.json` at the pack root.
2. The pack registers **disabled**.
3. **Enable** (requires license **entitlement** if the pack declares one).
4. Configure non-secret keys and write-only secrets in the pack detail UI.
5. **Disable** keeps config + secrets; **uninstall** wipes them.

Sample fixture for smoke tests:

```text
packs/fixtures/content-publisher
```

See that folder’s README for detailed smoke steps.

## Manifest (user-relevant fields)

| Field | Meaning |
|-------|---------|
| `id` / `name` / `version` | Identity |
| `min_app_version` | Compatibility floor |
| `entitlement` | License claim required to enable (e.g. `pack.content-publisher`) |
| `permissions` | Host capabilities (`net`, `config`, `secrets`, `mcp`, …) |
| `config` | Declared config keys (scope, type, secret) |
| `mcp_servers` | Pack-owned stdio MCP processes |
| `skills` / `workflows` | Documentation lists; **discovery is disk scan** when enabled |

### Disk layout when enabled

```text
{pack}/skills/*/SKILL.md          # or sealed meta.json + body.sealed
{pack}/workflows/*.json           # workflow definitions
{pack}/mcp/...                    # optional MCP server code
```

## Config & secrets

| Kind | UI / API behavior |
|------|-------------------|
| Config (non-secret) | Editable; may be **app** or **workspace** scoped |
| Secret | **Write-only**; list shows `is_set`, never the value |

Materialized runtime files live under app data (e.g. `packs-runtime/<pack-id>/`) — secrets are **not** written into those JSON blobs.

Workspace-scoped keys need an **open workspace**.

## Entitlements & license

- Dev mock keys (Settings → License): `DNT-BASE`, `DNT-BASE-CONTENT`, `DNT-EXPIRED`.
- Example: content-publisher needs entitlement `pack.content-publisher` → activate `DNT-BASE-CONTENT`.
- Losing entitlement (refresh/deactivate) **force-disables** enabled packs and **cancels** their workflow runs; MCP contributions drop on reload.

Core app chat works without a license key; pack features may not.

## Workflows

When a pack is enabled, **Settings → Packs → Workflows** lists definitions from disk.

### Running

- Open a **workspace** (required to start runs).
- Start from the Packs UI (e.g. **Run demo workflow**) or ask the agent to use:
  - `workflow_list`
  - `workflow_start`
  - `workflow_status`
  - `workflow_cancel`

### Human review

Steps of type **human_review** pause the run. Use the **Review panel** to edit payloads and **approve** / **reject**.

Modes do **not** auto-complete human review — it is always UI-driven.

### Demo workflows (content-publisher fixture)

| Workflow id | Purpose |
|-------------|---------|
| `demo-setvars-review` | set_vars → human_review → set_vars (primary smoke) |
| `demo-tool-list-dir` | Built-in `list_dir` + review |
| `publish-from-url` | Host `http_request` GET → sealed LLM skill → review → POST (needs pack `net`) |
| `publish-from-url-mcp` | Pack MCP `fetch_url` → same LLM/review → MCP `deliver_webhook` (needs Node) |

`publish-from-url` remains useful for Node-free CI of the pipeline shape even when MCP is connected.

## Agent tools vs UI

| Action | Agent tool | Notes |
|--------|------------|-------|
| Discover | `workflow_list` | Auto in all modes that allow it |
| Start | `workflow_start` | Approval in Ask / Edit automation; auto in Full access; hidden in Plan |
| Poll | `workflow_status` | Auto |
| Cancel | `workflow_cancel` | Same approval rules as start |

## Related

- Fixture deep-dive: [`packs/fixtures/content-publisher/README.md`](../../packs/fixtures/content-publisher/README.md)  
- [Skills](./skills.md) · [MCP](./mcp.md) · [Tools & approvals](./tools-and-approvals.md)  
