# Project instructions

Project instructions are **workspace-level** rules injected into the agent system prompt for every turn in that folder.

## Where to put them

First file found wins (prefer the first):

1. `{workspace}/AGENTS.md`
2. `{workspace}/.dntspace/instructions.md`

Cap: **32 KiB** injected into the system prompt (larger files are truncated).

## How layers combine

System prompt layer order (conceptually):

```text
base → agent mode addon → profile system prompt → project instructions → skills catalog → workspace tree summary
```

- **Profile system prompt** — Settings → System prompt (per profile)  
- **Project instructions** — this page (per workspace folder)  
- **Skills catalog** — short name/description list; full body via `load_skill`

Use project instructions for **repo-specific** conventions. Use profile instructions for **personal / role** defaults that should follow you across folders.

## What to write

Good candidates:

- Build / test / lint commands for this repo  
- Code style and PR expectations  
- Forbidden paths or “do not touch” areas  
- Domain glossary  
- Preferred tools or workflows  

Example `AGENTS.md` sketch:

```markdown
# Agent notes

## Verify
- Frontend: `npm test`
- Rust: `cd src-tauri && cargo test`

## Style
- Match surrounding comment density and naming.
- Prefer focused diffs; do not reformat unrelated files.

## Safety
- Never commit secrets.
- Do not force-push shared branches.
```

## Skills vs AGENTS.md

| Prefer | When |
|--------|------|
| `AGENTS.md` | Always-on repo rules, short and high signal |
| Skills | Optional playbooks loaded when relevant (`$skill` / `load_skill`) |

Do not duplicate a long skill body into `AGENTS.md` — keep the always-on prompt lean.

## Related

- [Skills](./skills.md)  
- [Agent guide](./agent-guide.md)  
- [Profiles & workspaces](./profiles.md)  
