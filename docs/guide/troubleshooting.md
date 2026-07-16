# Troubleshooting

## App will not build or start

| Symptom | Things to check |
|---------|-----------------|
| Rust / link errors on Windows | MSVC **Desktop development with C++**, Windows SDK, WebView2 |
| `npm run tauri dev` fails early | Node 22+, `npm install`, free ports **5210** / **5211** |
| Vite alone works but no backend | Use `npm run tauri dev`, not only `npm run dev` |

## Provider / model errors

- Confirm **base URL** is OpenAI-compatible and includes the correct path prefix for your host.
- Re-paste API key; use **Test connection**.
- Check the model is **enabled** in Settings → Models.
- 429 / 5xx: wait and retry; the app may auto-retry once.

## Agent cannot see or edit files

- Is a **folder open** for this profile?
- Paths must stay under the workspace root.
- In **Plan** mode, write tools are unavailable — switch mode.
- Approvals pending? Approve or change mode / Always allow.

## MCP server failed

- Command available on `PATH`? (e.g. `npx`, `node`, `uvx`)
- Args correct? Working directory assumptions?
- Click **Reconnect** after fixes.
- Multi-window: last reconnect wins for the whole process — reconnect from the profile you care about last.
- Pack MCP: enable fails if connect fails (e.g. content-publisher needs Node 18+ as `node`).

## Skills missing

- Disabled in **Settings → Skills** for this profile?
- Pack skill: is the pack **enabled** and entitled?
- Wrong name? Catalog uses frontmatter `name`.
- Workspace override hiding a pack/bundled skill with the same id?

## Packs / workflows

| Issue | Check |
|-------|--------|
| Cannot enable pack | License entitlement; mock `DNT-BASE-CONTENT` for sample content packs |
| Workflow start fails | Workspace open? Pack still enabled? |
| Stuck awaiting human | Review panel approve/reject |
| Webhook / publish demos | Config `webhook.url`, secret token, permission `net`, Node for MCP path |

## Performance / context

- Large workspaces: mention specific `@files` instead of relying on the whole tree summary.
- Long threads: start a new conversation when context is noisy.
- Set per-model context limits in Models settings if the UI supports it for your provider.

## License

- Core features should work without a key.
- Pack-only features need the right entitlement; `DNT-EXPIRED` should fail closed for entitled packs.

## Logs & diagnostics

- Open **Settings → Diagnostics** to view the recent log tail, toggle **Verbose logging**, export the log, or **Report issue** (creates a local zip — nothing is uploaded).
- Log file (app-global): under the app data directory in `logs/dntspace.log` (rotated `.1`–`.3`).
- Verbose logging adds debug detail (paths, timings); secrets stay redacted and chat message bodies are not written to logs.
- Developers can still set `RUST_LOG` (overrides the in-app verbose filter).
- When contacting support: attach the Report issue zip and paste the Copy summary text.

## Still stuck

1. Note the exact UI error string.
2. Retry after restart (clears session Always allow and in-process MCP registry).
3. For contributors: `cd src-tauri && cargo test`, `npm test`, and design notes under `docs/superpowers/`.
