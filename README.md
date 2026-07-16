# DntSpace Releases

**Public downloads and user documentation** for [DntSpace](https://github.com/dungnt1312/dntspace-releases).

> Source code is **private**. This repository publishes installers, checksums, release notes, and the user guide only.

## Download

| Version | Assets |
|---------|--------|
| **[v0.1.0](https://github.com/dungnt1312/dntspace-releases/releases/tag/v0.1.0)** | Windows NSIS setup, MSI, SHA256 |

Always prefer the [Releases](https://github.com/dungnt1312/dntspace-releases/releases) page for the latest build.

### Verify (optional)

```bash
# after download, compare with SHA256SUMS.txt from the same release
sha256sum DntSpace_0.1.0_x64-setup.exe
# or on Windows PowerShell:
# Get-FileHash .\dntchat_0.1.0_x64-setup.exe -Algorithm SHA256
```

## What is DntSpace?

Local desktop **AI agent workspace**: open a folder, chat with OpenAI-compatible models, and let the agent use tools (files, shell, skills, packs/workflows) under a path sandbox.

- Profiles, agent modes, approvals  
- Skills, MCP, packs & workflows  
- Windows-first (WebView2)

## Documentation

| Topic | Link |
|-------|------|
| Install & first run | [docs/guide/getting-started.md](docs/guide/getting-started.md) |
| Full user guide | [docs/guide/README.md](docs/guide/README.md) |
| Changelog | [CHANGELOG.md](CHANGELOG.md) |

## System requirements (Windows)

- Windows 10/11 x64  
- [WebView2 Runtime](https://developer.microsoft.com/microsoft-edge/webview2/) (usually preinstalled)  
- Network access for your chosen model provider  

## Support / issues

Use your private support channel or open a discussion on this repo if enabled. Do not expect source builds from this repository.

## License / product

Closed-source desktop application. Redistribute only the official installers from this Releases page unless you have a separate agreement.
