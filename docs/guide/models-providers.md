# Models & providers

Configure under **Settings → Models** (app-wide).

## Add a provider

1. Open **Settings → Models**.
2. Add a **preset** (known OpenAI-compatible base URL) or **custom** provider.
3. Set:
   - **Name** — label in the UI  
   - **Base URL** — OpenAI-compatible API root  
   - **API key** — stored in OS keychain when possible  
4. Save, then use **Test connection** when offered.
5. Refresh / enable models you want in the model picker.

Providers are shared across all profiles. Per-turn model choice still happens in the chat composer.

## Choosing a model in chat

- Composer model picker (shortcut: **Ctrl/Cmd+Shift+M** by default).
- Provider → model for the next turn.
- Context usage / limits may appear when configured (per provider-model limit map in Models settings).

## Media tools (image / video)

Under **Settings → Models → Media tools**:

| Kind | Tools | Configure |
|------|--------|-----------|
| Image | `generate_image`, `edit_image` | Image provider + model (e.g. `dall-e-3`, `gpt-image-1`) |
| Video | `generate_video` | Video provider + model (e.g. provider-specific id) |

Generated media is stored under app data (e.g. `generated-images/`). The UI can preview and open the folder; the agent should not dump raw save paths as the primary UX (see [Agent guide](./agent-guide.md)).

## API keys & storage

- Preferred: OS keychain, service `dntspace`, account `provider:{id}`.
- Fallback: local SQLite / app data.
- Keys can be revealed and edited in Settings (treat the machine as trusted).

## Tips

- Prefer **Test connection** after changing base URL or key.
- Use a cheaper/faster model for Plan / research; a stronger model for Edit automation.
- Image/video tools only work after media provider+model are set — chat models alone are not enough.
