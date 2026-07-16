# Profiles & workspaces

## Profiles

A **profile** is an isolated use-case context:

| Stored per profile | Examples |
|--------------------|----------|
| Chat history | Conversations and messages |
| Default agent mode | Ask before edit, Plan, … |
| Skills disable list | Which skills are turned off |
| MCP **config** | Server definitions for this profile |
| Custom system prompt | Settings → System prompt |
| Open folder | Last workspace root for this profile |
| **Vault secrets** | API tokens, passwords for this profile (Settings → Vault; agent `vault_get` / `vault_apply`) |

**One open profile = one window.** On launch, dntspace restores previously open profiles.

### Manage profiles

- Use the **profile chip** in the UI to create, open, or manage profiles.
- Existing data from older installs migrates into a profile named **Default**.
- Creating a profile can use the create wizard (name, defaults, optional model).

### Multi-window note

You can open the **same folder** in multiple profiles. Each profile keeps its own history, mode, MCP config, skill disable list, and vault.

**MCP live registry caveat:** each profile stores its own `mcp_config_json`, but the in-process MCP tool registry is still **process-shared**. The last reconnect/save wins for all open windows. Per-runtime isolation is a known follow-up.

## Workspaces (folders)

**Open Folder** sets the workspace root for the current profile.

That root drives:

- File tree and Monaco editing
- Path sandbox for file tools
- Project instructions (`AGENTS.md` or `.dntspace/instructions.md`)
- Workspace skills under `.dntspace/skills/`
- Workspace-scoped pack config/secrets (when packs use workspace scope)

### Path sandbox (what “safe” means)

- File tools resolve paths under the workspace root only.
- `run_command` runs with **cwd = workspace root**.
- Absolute paths or `..` segments that escape the root are **denied** (heuristic checks — **not** a full OS process sandbox).

Do not treat agent shell access as fully isolated from the host.

## What is global vs per-profile?

| Global (app) | Per profile |
|--------------|-------------|
| Providers & API keys | Conversations |
| Appearance / keyboard prefs | Agent mode default |
| Packs install/enable | Skills disable list |
| License | MCP server **config** |
| Image/video tool model pickers | System prompt / instructions |
| | Open workspace folder |
| | **Vault secrets** (tokens/passwords; not provider keys) |

## Practical recipes

**Separate “coding” and “writing” contexts**  
Create two profiles. Same or different folders; different models, modes, MCP, and skills.

**Shared repo, private history**  
Open the same folder in two profiles. Histories stay separate.

**Reset agent behavior without losing app providers**  
Adjust that profile’s System prompt, Skills disable list, and agent mode — providers stay shared.
