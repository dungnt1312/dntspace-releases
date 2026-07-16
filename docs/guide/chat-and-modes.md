# Chat & agent modes

## Conversations

- Multiple conversations per **profile**, stored in SQLite.
- Streaming replies with **Stop** (default: **Esc** while generating).
- Assistant **thinking / reasoning** streams when the provider sends it.
- Attachments: paste/drag images and files where supported; long paste may become a file attachment.

## Composer mentions

| Trigger | Purpose |
|---------|---------|
| `@path` | Mention a workspace file (picker filters tree paths). |
| `$skill-name` | Tag an enabled skill (Codex-style). Loads skill intent into the turn. |
| `/command` | Slash commands when available (picker competes with `@` / `$`). |

Rules of thumb:

- Closest token to the cursor wins; tie-break order is **`@` > `$` > `/`**.
- `$` must start at the beginning of the input or after whitespace.
- Skill picker only lists **enabled** skills for the profile (respects disable list and pack enable state).

## Agent modes

Pick the mode on the composer. Default: **Ask before edit** (`ask_before_edit`).

| Mode | ID | Behavior |
|------|-----|----------|
| **Ask before edit** | `ask_before_edit` | Read tools auto-run. Writes, shell, media, workflows start/cancel, MCP → **need approval**. |
| **Plan** | `plan` | Research only. Reads + skill load + workflow list/status. **No** writes, shell, media, MCP, workflow start/cancel. |
| **Edit automation** | `edit_automation` | File edits and media auto. **Shell**, **workflow start/cancel**, and **MCP** still need approval. |
| **Full access** | `full_access` | Tools auto **within policy** (including shell, MCP, workflow start/cancel). Shell still has **host privileges**. **Dangerous** shell still needs approval. Paths **outside** the workspace still need approval (OpenCode-style). |

### When to use which

- **Exploring a repo / drafting a plan** → Plan  
- **Day-to-day coding with guardrails** → Ask before edit  
- **Trusted, iterative edits** → Edit automation  
- **You want maximum speed and accept risk** → Full access  

Workflow **human_review** steps always go through the UI Review panel — modes do not auto-approve those.

## Approvals in chat

When a tool needs approval, a tool card shows **Needs approval**:

- **Approve** / **Reject** for that call  
- **Always allow** (session) — process memory until restart, **not** permanent:
  - File / MCP / other: remembers **tool name**
  - `run_command`: remembers **command prefix** (first two tokens), not all shell
  - Dangerous shell: never session-always (Always ≡ Approve once)

If a **subagent** needs approval, the same bar appears on the main chat with a **source** label (nickname / role / agent path) so you know which child requested it.

Details: [Tools & approvals](./tools-and-approvals.md).

## Context usage ring

The composer **always shows** a context-window usage ring for the active conversation (estimate and/or last API usage).

- Tone: ok below 70%, warn ≥ 70%, danger ≥ 90%.
- At **≥ 70%**, the UI **suggests compact** (summarize older history via `/compact` or the compact control).

## Multi-agent threads (subagents)

dntchat supports **Codex-style subagents**: the **root** model orchestrates children by calling collab tools (`spawn_agent`, `wait`, `send_message`, `list_agents`, `close_agent`, `interrupt_agent`). You do not pick subagents from a menu — the model spawns them when parallel work helps (especially research/explore).

Multi-agent is **always on** for now (no Settings toggle). Single-agent chats are unchanged until the model actually spawns children.

### Caps

| Cap | Value | Meaning |
|-----|-------|---------|
| **Max depth** | `1` | Only the root can spawn. Children cannot nest further. |
| **Max threads** | `6` | Root + children in one conversation tree (including root). Further spawns fail with a tool error. |

### Roles

| Role | Behavior |
|------|----------|
| **explorer** | Read-only research. Hard-denies write/edit/delete/patch/dir mutations, media, shell, and other mutators — even if the parent mode is Full access. |
| **worker** | Implementation-style child. Inherits parent mode/tool matrix (minus collab tools). |
| **default** | General-purpose child. Same inherit rules as worker. |

Children never receive collab tools; only the root coordinates the tree.

### What you see

- **Main chat** stays root-only: your messages, the orchestrator’s replies, and root tool cards (including collab tools).
- **Subagents panel** lists children (nickname, role, path, status). Open a child for its transcript and interrupt/close actions.
- Children are **not** separate sidebar conversations.

### Stop and cancel

- **Stop on the root** (Esc while generating) cancels the root **and cascade-cancels** every running child in the tree.
- Interrupt/close on a **single child** affects only that child; the root can continue.
- If the root generation ends while children are still running, remaining children are cascade-cancelled (fail-safe).
- A `wait` timeout does **not** cancel children; it returns `still_running` so the parent can wait again.

### Cost warning

Each subagent runs its **own model loop** with tools. Parallel explorers multiply token usage and latency versus a single agent. Prefer explorers for research, keep briefs focused, and avoid spawning more children than the task needs.

### When it helps

- Parallel codebase exploration (multiple `explorer` agents, root synthesizes)
- Split research vs implementation (`explorer` + `worker`) under one user turn

Stay in **Plan** or **Ask before edit** when you want tight guardrails; explorers remain read-only regardless of mode.

## Reliability behaviors (user-visible)

- Stop cancels in-flight generation (**and the whole multi-agent tree** when stopping the root).
- Transient provider errors (e.g. 429 / 5xx) may be retried once.
- Partial content may be saved if a stream fails mid-reply.
- History is managed so orphan tool messages are avoided when possible.

## Tips

- Mention files with `@` instead of pasting huge blobs when the agent can `read_file`.
- Use `$skill` when you want a specific playbook without waiting for the model to discover the catalog.
- Switch to Plan before large refactors if you want a design first with no accidental writes.
- For multi-agent runs, watch the Subagents panel and approval **source** labels; Stop on root if you want the whole tree to halt.
