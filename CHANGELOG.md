# Changelog

All notable **user-facing** changes to DntSpace are listed here.
Internal plans and private repo details stay out of this file.

Format: version sections newest-first. Use semver `MAJOR.MINOR.PATCH`.

## 0.1.0 — local MVP

### Features

- Desktop app (Tauri): profiles, workspace sandbox, chat with agent modes
- Provider configuration and local key storage
- Built-in tools, approvals, skills/packs (as enabled)
- Diagnostics / local issue report zip

### Known limitations

- No in-app auto-update — install a newer build manually
- Shell policy is heuristic, not an OS sandbox
- See product safety docs for residual risk

### Distribution

- Closed source; public artifacts are installers only (NSIS/MSI)
- Product rename: **DntSpace** (formerly dntchat)
