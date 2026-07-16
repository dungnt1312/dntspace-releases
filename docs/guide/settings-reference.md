# Settings reference

Open Settings with **Ctrl/Cmd+,** (default) or the UI entry. **Esc** closes the modal.

Navigation is split into **App** and **Profile** groups.

## App

### Appearance

Theme and UI presentation preferences (light/dark/system as offered by the build).

### Keyboard

Rebind global shortcuts. Composer **Enter** / **Shift+Enter** are fixed.

- Click a command → press a new chord  
- **Esc** cancels capture  
- **Backspace/Delete** disables that shortcut  
- Conflicts are detected before apply  
- Reset one command or reset all  

Details: [Keyboard shortcuts](./keyboard-shortcuts.md).

### License

Activate / view license status, usage, and hosted models. Default **Server URL**: `http://127.0.0.1:8787` (local `dntlicd`). Hosted chat uses `{server}/llm` with the session token (synthetic provider **DNT Hosted** / `dnt-hosted` in the model picker).

| Dev mock key | Role |
|--------------|------|
| `DNT-BASE` | Base entitlement sample (tests / mock mode only) |
| `DNT-BASE-CONTENT` | Content pack entitlement (e.g. content-publisher) |
| `DNT-EXPIRED` | Expired key for testing |

Core chat and workspace tools work **without** a key. Pack enablement may require entitlements. Policy is driven by `license_policy_json` (default may allow agent without base license). Live server ops: [server/README.md](../../server/README.md).

### Packs

Import, enable/disable, uninstall packs; edit config keys and write-only secrets; list/run workflows.

→ [Packs & workflows](./packs-and-workflows.md)

### Models

Providers (preset or custom base URL), API keys, model enablement, connection test, optional per-model context limits, and **Media tools** (image/video provider+model).

→ [Models & providers](./models-providers.md)

## Profile

These settings apply to the **current profile** only.

### System prompt

Custom profile instructions (markdown editor). Combined with mode, project instructions, and skills catalog in the system prompt.

### MCP

Per-profile MCP server definitions (stdio). Live registry is still process-global — see [MCP servers](./mcp.md).

### Skills

Catalog list, enable/disable, edit bodies, sealed/protected indicator, source badges.

→ [Skills](./skills.md)

### Vault

Per-profile user secrets (list, add/edit, reveal, delete). Separate from provider API keys and pack secrets. Agent tools: `vault_get` / `vault_apply`.

→ [Profile vault](./vault.md)

## Outside Settings (but related)

| Control | Where |
|---------|-------|
| Agent mode | Chat composer |
| Model picker | Chat composer |
| Open folder | Toolbar / shortcut |
| Profile create/open/manage | Profile chip |
| Tool approvals / Always allow | Tool cards in chat |
| Workflow human review | Review panel |

## Related

- [Profiles & workspaces](./profiles.md)  
- [Getting started](./getting-started.md)  
