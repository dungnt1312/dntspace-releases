# Tools & approvals

Built-in tools are registered in the agent loop and filtered by **agent mode**. MCP tools appear as `mcp__{serverId}__{toolName}` when servers are connected.

## Built-in tools (overview)

### Read / research (usually Auto)

| Tool | Role |
|------|------|
| `list_dir` | List a directory under the workspace |
| `read_file` | Read file contents |
| `search` | Filtered content search |
| `glob` | Find files by pattern |
| `git_status` | Read-only git status |
| `git_diff` | Read-only git diff |
| `load_skill` | Load full skill body by name |
| `workflow_list` | List workflows from enabled packs |
| `workflow_status` | Status of a workflow run |

### Write / lifecycle

| Tool | Role |
|------|------|
| `write_file` | Create/overwrite a file |
| `edit_file` | Edit a file |
| `multi_edit` / replace-style helpers | Multi-hunk / multi-file style edits |
| `apply_patch` | Apply a unified diff (multi-file) |
| `delete_file` / `rename_file` | File lifecycle |
| `create_dir` / `delete_dir` | Directory lifecycle |

### Shell

| Tool | Role |
|------|------|
| `run_command` | Run a command with cwd = workspace root |

### Media & artifacts

| Tool | Role |
|------|------|
| `generate_image` / `edit_image` | Image tools (need Media settings) |
| `generate_video` | Video tool (need Media settings) |
| `send_artifact` | Attach a durable artifact (HTML/markdown/code/file) in chat |

### Workflows

| Tool | Role |
|------|------|
| `workflow_start` | Start a pack workflow (may pause for human review) |
| `workflow_cancel` | Cancel a run |
| `workflow_list` / `workflow_status` | Discovery and polling |

### Profile vault

| Tool | Role |
|------|------|
| `vault_get` | Read this profile’s vault (`list` / `get` / `has`). **`get` returns secret plaintext.** |
| `vault_apply` | Write this profile’s vault (`set` / `delete`). `set` does **not** echo the value. |

Vault is **per profile** and separate from provider API keys and pack secrets. See [Profile vault](./vault.md).
### Multi-agent collab (root only)

Codex-style subagents. The **root** model spawns and coordinates children; you do not start them from a picker. Multi-agent is **always on** (no prefs flag yet). Caps: **max depth 1**, **max threads 6** (including root). See [Chat & agent modes](./chat-and-modes.md#multi-agent-threads-subagents).

| Tool | Role |
|------|------|
| `spawn_agent` | Create a child thread + start its generation (`default` / `explorer` / `worker`) |
| `wait` | Wait for one or more children (timeout returns `still_running`; does **not** cancel) |
| `send_message` | Send additional input to an open child |
| `list_agents` | List threads in the tree (status, path, role, nickname) |
| `close_agent` | Close a child (cancel if still running) |
| `interrupt_agent` | Interrupt a child’s generation; thread stays inspectable |

Children **do not** receive collab tools. **explorer** is hard read-only (writes, shell, media, and other mutators denied even under Full access).

Host-only tools such as `http_request` may exist for **workflow packs** (permission-gated) and are not always exposed as free-form chat tools.

## Mode × policy matrix (summary)

| Tool group | Ask before edit | Plan | Edit automation | Full access |
|------------|-----------------|------|-----------------|-------------|
| Read / search / git / load_skill / workflow list+status | Auto | Auto | Auto | Auto |
| `vault_get` (list / get / has; **get returns secret plaintext**) | Auto | Auto | Auto | Auto |
| File write / lifecycle / apply_patch | Approve | Hidden | Auto | Auto |
| Media / send_artifact | Approve | Hidden | Auto | Auto |
| `run_command` | Approve | Hidden | **Approve** | Auto |
| `workflow_start` / `workflow_cancel` | Approve | Hidden | **Approve** | Auto |
| `vault_apply` (set / delete) | Approve | Hidden | Auto | Auto |
| MCP `mcp__*` | Approve | Hidden | **Approve** | Auto |
| Collab (`spawn_agent`, `wait`, …) — **root only** | Auto | Auto | Auto | Auto |

Exact lists live in the Rust mode policy; when in doubt, treat **Plan** as read-only.

### Full access (honest limits)

**Full access** auto-runs tools **within mode policy** (including shell, MCP, and workflow start/cancel). It is **not** a sandbox escape:

- **Shell still has host privileges** — `run_command` runs as your user on the machine (cwd = workspace root for relative work).
- **Dangerous shell** (heuristic: e.g. destructive `rm -rf`, remote code exec patterns) **still needs approval**, including in Full access.
- **Paths outside the workspace** still need approval (OpenCode-style), including for tools that would otherwise be Auto.

## Outside workspace (OpenCode-style)

File tools and `run_command` may use absolute paths (or `..`) **outside** the active workspace root.

| Gate | Behavior |
|------|----------|
| Outside workspace | **Always needs approval**, including Full access and Auto tools (e.g. `read_file`) |
| Always allow (outside) | Remembers `{parentDir}/*` for this profile until app restart — not the whole disk |
| Workspace UI / tree | Still hard-jailed (cannot browse outside from the Files panel) |

Shell path scanning is heuristic (not a full parser). Prefer Ask mode when the model or MCP is untrusted.

## Approvals UX

1. Tool card enters **awaiting_approval**.
2. User chooses:
   - **Approve** — run once  
   - **Reject** — deny this call  
   - **Always allow** — session memory until app restart (see below)  

### Always allow (session memory)

**Always allow** is process memory only. It is not written as a permanent policy and is not per-workspace. Cleared on app restart.

| Kind | What is remembered |
|------|--------------------|
| File / MCP / other gated tools | **Tool name** for the rest of the app run |
| `run_command` | **Command prefix** = first two whitespace-separated tokens (e.g. `git status`), **not** all shell |
| Dangerous shell | **Never** session-always — Always behaves like Approve once |
| Outside workspace | `{parentDir}/*` folder pattern for this profile until restart |

### Subagent approvals bubble to root

Approvals from **child threads** use the same main-chat ApprovalBar. The card shows a **source** label (e.g. nickname + role, with agent path on hover) so you can tell which subagent requested the tool. Queue is FIFO when several are pending. **Always allow** still updates the shared session allowlist (siblings can benefit). **Reject** returns a tool error on the requesting thread.

## Cancellation

| Trigger | Behavior |
|---------|----------|
| **Stop root** | Cancels root generation **and cascade-cancels** all running children |
| Interrupt/close one child | That child only |
| Root generation ends with live children | Cascade cancel remaining children |
| `wait` timeout | No cancel — children keep running |

## Safety expectations

- Inside the workspace, path tools stay under the root for normal relative paths.
- Outside the workspace requires an explicit approval (or session Always allow for that folder pattern).
- Shell is powerful even with path heuristics — prefer Ask / Edit automation over Full access when untrusted.
- Malicious MCP servers are high risk — prefer Ask (or Edit automation) so MCP calls need approval; only Full access auto-runs MCP.
- Pack network tools require pack permissions (e.g. `net`) and entitlements.
- `vault_get` can return secrets into the tool result / chat transcript for this profile — do not put them in MEMORY or public files.
- **Token cost:** each subagent is a separate model loop. Parallel children multiply usage; prefer focused `explorer` briefs and avoid unnecessary spawns.
- Parallel **writers** on the same files are not locked in v1 — the orchestrator should avoid concurrent edits to the same paths (explorers help by staying read-only).

## For agents using tools

See [Agent guide](./agent-guide.md). Short version:

- Prefer `read_file` / `search` / `glob` before large rewrites.
- Use `apply_patch` or focused edits over rewriting whole files when possible.
- Do not claim tests passed without running them (`verification-before-completion` skill).
- Do not re-`send_artifact` generated media just to redisplay a path the UI already previews.
- For multi-agent: spawn only from the root, respect depth/thread caps, prefer `explorer` for research, `wait` before the final user answer, and never assume children can spawn further agents.
