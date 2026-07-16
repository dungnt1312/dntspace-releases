# Getting started

## Install (Windows)

1. Open **[Releases](https://github.com/dungnt1312/dntspace-releases/releases)** and download the latest **setup** installer (NSIS `.exe`) or **MSI**.
2. (Optional) Verify the file hash against `SHA256SUMS.txt` attached to the same release.
3. Run the installer. If Windows SmartScreen warns on an unsigned build, choose **More info → Run anyway** only if you trust the publisher channel.
4. Launch **DntSpace** from the Start menu.

### Prerequisites

| Requirement | Notes |
|-------------|--------|
| Windows 10/11 x64 | First-class platform for 0.1 |
| WebView2 | [Download](https://developer.microsoft.com/microsoft-edge/webview2/) if missing |

macOS / Linux installers are not published in 0.1.

## First launch

1. **Models** — open **Settings → Models**, add an OpenAI-compatible provider (or preset), paste an API key, pick a model. Use **Test connection** when available.
2. **License (optional)** — core chat and workspace tools work without a key. Activate under **Settings → License** when you need pack entitlements.
3. **Open a folder** — toolbar / **Ctrl+O**. This is the workspace root for the current profile (path sandbox).
4. **Chat** — create or select a conversation, choose a model and agent mode (**Ask before edit** is the safe default), send a message.

API keys prefer the OS keychain (service `dntspace`); otherwise they fall back to local app data.

## UI layout (3 columns)

1. **Sessions** — conversations for the current profile  
2. **Chat** — messages, tool cards, composer  
3. **Workbench** — explorer / editor / related panels  

See [Keyboard shortcuts](./keyboard-shortcuts.md).

## What to configure next

- Safer defaults: keep **Ask before edit** → [Chat & agent modes](./chat-and-modes.md)
- Project rules for the agent → [Project instructions](./project-instructions.md)
- Reusable skill packs → [Skills](./skills.md)
- Packs & workflows → [Packs & workflows](./packs-and-workflows.md)

## Data location

See [Data storage](./data-storage.md). Default home is under `~/.dntspace` (or the app data directory for the bundle id `com.dntspace.app` on Windows).

## Updating

There is no in-app auto-update in 0.1. Download a newer installer from Releases and install over the previous version. App data is kept unless a release notes a breaking path change.

## Troubleshooting install

| Symptom | What to try |
|---------|-------------|
| App won’t start | Install/repair WebView2; reboot once |
| SmartScreen block | Expected for unsigned early builds; use official Releases only |
| Old data missing after rename | 0.1 rebrand may use new app id/paths — see [Data storage](./data-storage.md) |

More: [Troubleshooting](./troubleshooting.md).
