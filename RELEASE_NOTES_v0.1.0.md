## DntSpace v0.1.0 — local MVP

First public **binary-only** drop. Source remains private.

### Install (Windows x64)

1. Download **NSIS** `*_x64-setup.exe` (recommended) or **MSI**.
2. Optional: verify SHA-256 using `SHA256SUMS.txt`.
3. Run installer → start **DntSpace**.

> **Note:** This build’s installer filenames may still use the pre-rebrand `dntchat_0.1.0_*` basename. The product is **DntSpace**. Future builds use `DntSpace_*` names.

### Highlights

- Local desktop agent workspace (profiles, modes, tools, approvals)
- OpenAI-compatible providers; keys in OS keychain when available
- Skills, MCP (stdio), packs & workflows (early)
- Path sandbox for file tools; heuristic shell policy (not an OS sandbox)

### Docs

- User guide in this repo: [`docs/guide/`](https://github.com/dungnt1312/dntspace-releases/tree/main/docs/guide)
- [Changelog](https://github.com/dungnt1312/dntspace-releases/blob/main/CHANGELOG.md)

### Known limitations

- No in-app auto-update — install newer builds manually  
- Unsigned installer may trigger SmartScreen  
- Shell/MCP residual risk — use **Ask before edit** on untrusted folders  
- Pre-release data paths may not migrate from older internal builds  

### Requirements

Windows 10/11 x64 + WebView2.
