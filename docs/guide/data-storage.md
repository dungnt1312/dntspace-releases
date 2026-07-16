# Data storage

DntSpace stores local desktop data in a dedicated DntSpace home directory.

Default location:

```text
~/.dntspace
```

Override location:

```text
DNTSPACE_HOME=/path/to/dntspace-home
```

`DNTSPACE_HOME` may be an absolute path or a path with a leading `~`. Relative `DNTSPACE_HOME` paths are rejected.
DntSpace creates the directory when it starts. If the resolved path exists and is not a directory, startup fails with a clear error.

## Layout

```text
~/.dntspace/
  dntspace.db
  dntspace.log
  conversations/
  profiles/
  packs/
  file-snapshots/
```

Main contents:

| Path | Purpose |
|------|---------|
| `dntspace.db` | SQLite database for settings, providers, profiles, conversations, messages, tool invocations, attachments, usage, packs, and workflows. |
| `dntspace.log` | Local app log file. |
| `conversations/` | Conversation file stores such as media, uploads, artifacts, and long-paste files. |
| `profiles/` | Profile instructions and bootstrap files. |
| `packs/` | Installed pack files. |
| `file-snapshots/` | Short-lived pre-write snapshots for agent undo/review flows. |

## Secrets

Provider API keys, pack secrets, and **profile vault** values prefer the OS keyring. The SQLite database stores metadata or fallback values depending on keyring availability.

Vault keyring accounts use `vault:{profile_id}:{key}` and are isolated per profile. See [Profile vault](./vault.md).

## Pre-release data

DntSpace does not migrate data from the old Tauri app data directory. The app was not live when the default changed, so existing developer data can be manually copied or deleted if needed.
