# Profile vault

The **vault** is a per-profile store for user-owned string secrets (API tokens, passwords, connection strings). Each profile has its own isolated vault; profile A never sees profile B’s keys or values.

Values prefer the **OS keyring** (account `vault:{profile_id}:{key}`). Metadata lives in SQLite. If the keyring is unavailable, values fall back to SQLite plaintext (same idea as provider keys).

## What vault is for

| Use vault for | Do **not** use vault for |
|---------------|--------------------------|
| Personal API tokens for scripts the agent runs in chat | **Provider API keys** (Settings → Models — app-global) |
| Passwords / connection strings for this use-case profile | **Pack secrets** (Settings → Packs — schema-bound, write-only to agent) |
| Credentials the agent should recall via tools | **MEMORY / bootstrap** (`MEMORY.md`, `SOUL.md`, …) — notes, not secrets |
| | Project files in the workspace (unless you intentionally put non-secrets there) |

Vault is **not** included in `profile_get` or the system prompt, so a normal profile snapshot will not dump secrets.

## Settings → Vault

Open **Settings → Vault** for the **current window’s profile**.

You can:

- **List** entries (key, description, timestamps — never values in the list)
- **Add / edit** a key, secret value, and optional description
- **Reveal** a value when you need to see it
- **Delete** an entry (with confirm)

### Key rules

| Rule | Detail |
|------|--------|
| Pattern | `^[a-zA-Z0-9._-]{1,64}$` (case-sensitive) |
| Value | Non-empty UTF-8 string, max **8 KiB** |
| Description | Optional, max **512** characters (not secret) |
| Empty value | Rejected — use **delete** instead of clearing |

Deleting a **profile** removes its vault rows (cascade) and best-effort keyring cleanup.

## Agent tools

The agent only sees the vault of the **conversation’s bound profile**. There is no `profile_id` argument; the host supplies it.

### `vault_get` (read)

| Op | Behavior |
|----|----------|
| `list` | Metadata only (`key`, `description`, `updated_at`); optional `query` substring filter |
| `get` | Full entry **including secret `value`** |
| `has` | `{ key, is_set: true/false }` |

### `vault_apply` (write)

| Op | Behavior |
|----|----------|
| `set` | Upsert key + value (+ optional description). Response: `key`, `stored`, `value_len` — **never** echoes the secret |
| `delete` | Remove entry; idempotent (`deleted: true/false`) |

### Mode matrix

| Tool | Ask before edit | Plan | Edit automation | Full access |
|------|-----------------|------|-----------------|-------------|
| `vault_get` | **Auto** | **Auto** | **Auto** | **Auto** |
| `vault_apply` | **Approve** | **Hidden** | **Auto** | **Auto** |

Same pattern as `profile_get` / `profile_apply`. Session **Always allow** applies like other gated tools (process memory until restart).

## Security warnings

1. **The agent can read vault values** in the bound profile (`vault_get` → `get` returns plaintext).
2. Tool results may appear in the **chat transcript** and model context for that conversation. Treat the model as trusted for that profile’s secrets.
3. Do **not** paste vault secrets into MEMORY, public files, or messages you would share outside this profile.
4. Prefer **Ask before edit** when you do not want the agent to write vault entries without approval.
5. Cross-profile access is blocked by host binding — not by the model “being careful.”

## Phase 2 (not v1)

Host inject is **not** implemented yet:

- No `{{vault.key}}` expansion in workflows
- No automatic MCP env injection from vault
- No expansion into `run_command` / `http_request` by key reference

The key namespace is reserved so those features can land later without renaming entries.

## Related

- [Profiles & workspaces](./profiles.md)
- [Tools & approvals](./tools-and-approvals.md)
- [Models & providers](./models-providers.md) (provider API keys stay separate)
- [Packs & workflows](./packs-and-workflows.md) (pack secrets stay separate)
- [Data storage](./data-storage.md)
