# Skills

Skills teach the agent **reusable workflows**. Enabled skills appear as a **catalog** in the system prompt; the full body is loaded on demand with the `load_skill` tool (Auto in all modes, including Plan).

Users can also tag a skill in the composer with **`$skill-name`**.

## Where skills live (precedence)

Higher layer wins when IDs collide:

```text
Folder (workspace)  >  Profile  >  Pack  >  Bundled
```

| Source | Location / notes |
|--------|------------------|
| **Folder** | `{workspace}/.dntspace/skills/<id>/SKILL.md` |
| **Profile** | Profile-scoped skill storage (when used) |
| **Pack** | `{pack}/skills/*/SKILL.md` while the pack is **enabled** (disk scan; manifest `skills` is documentation only) |
| **Bundled** | Shipped with the app, e.g. `verification-before-completion` |

Example: a workspace copy of `verification-before-completion` overrides the bundled skill with the same id.

## SKILL.md format

Each skill is a folder with `SKILL.md`:

```text
{workspace}/.dntspace/skills/<id>/SKILL.md
```

YAML frontmatter requires **`name`** and **`description`**. The markdown body is the full skill text.

```markdown
---
name: my-skill
description: One-line when-to-use guidance for the catalog.
---

# My skill

Step-by-step instructions the agent should follow when this skill is loaded.
```

### Sealed (protected) skills

Pack skills may ship as:

```text
meta.json + body.sealed   # AES-GCM sealed body
```

Settings shows **Protected** (no plaintext body in the UI). The model still receives the decrypted body **in memory** when the skill is loaded.

## Settings → Skills

**Settings → Profile → Skills**:

- List skills with source (folder / pack / bundled / …)
- **Enable / disable** per profile (disable list)
- **Edit** skill bodies when allowed

**Copy-on-write:** editing a bundled skill while a workspace is open **copies** it into `{workspace}/.dntspace/skills/<id>/`. Further edits stay in the workspace.

## How the agent uses skills

1. Catalog (name + description) is injected into the system prompt.
2. Agent calls `load_skill` with the skill **name** to get the full body.
3. User can force intent with `$skill-name` in the composer.

`load_skill` is available even in **Plan** mode so research can follow playbooks without writing files.

## Bundled sample

**`verification-before-completion`** — before claiming work is done, run relevant tests/build and report real output (or explicitly say verification was skipped).

## Authoring tips

- Keep `description` specific so the catalog helps the model pick the right skill.
- Put durable project process in workspace skills or `AGENTS.md` ([Project instructions](./project-instructions.md)).
- Prefer one skill = one job; avoid mega-skills that always load.
- Pack authors: public `SKILL.md` for demos; sealed skills for proprietary templates.

## Related

- [Packs & workflows](./packs-and-workflows.md) — pack-contributed skills  
- [Chat & agent modes](./chat-and-modes.md) — `$` mentions  
- [Agent guide](./agent-guide.md) — how agents should load skills  
