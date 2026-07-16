# DntSpace User Guide



Practical docs for **using and configuring** DntSpace — for people and for agents that need to know how the product works.



This is **not** design/architecture material. Specs and implementation plans live under [`docs/superpowers/`](../superpowers/).



## What is DntSpace?



DntSpace is a **local desktop AI agent workspace**. You open a folder, chat with an OpenAI-compatible model, and the agent can read/search/edit files (and run shell commands) inside that folder under a path sandbox.



Built with Rust + Tauri 2 + React. The UI is thin; providers, SQLite, tools, and the agent loop run in the Rust core.



## Start here



| If you want to… | Read |

|-----------------|------|

| Install and run the app | [Getting started](./getting-started.md) |

| Understand profiles & workspaces | [Profiles & workspaces](./profiles.md) |

| Store per-profile secrets (vault) | [Profile vault](./vault.md) |

| Add API keys / models | [Models & providers](./models-providers.md) |

| Chat, modes, @ / $ mentions, multi-agent | [Chat & agent modes](./chat-and-modes.md) |

| Tools, approvals, collab tools, Always allow | [Tools & approvals](./tools-and-approvals.md) |

| Teach the agent reusable workflows | [Skills](./skills.md) |

| Connect MCP servers | [MCP servers](./mcp.md) |

| Install packs & run workflows | [Packs & workflows](./packs-and-workflows.md) |

| Project rules (`AGENTS.md`) | [Project instructions](./project-instructions.md) |

| Full Settings map | [Settings reference](./settings-reference.md) |

| Shortcuts | [Keyboard shortcuts](./keyboard-shortcuts.md) |

| Something broke | [Troubleshooting](./troubleshooting.md) |

| You are an **agent** operating inside DntSpace | [Agent guide](./agent-guide.md) |



## Mental model (one screen)



```text

Profile  ──► conversations, agent mode, skills disable list, MCP config, system prompt, open folder, vault

   │

   ├── Workspace folder  ──► file tree, Monaco, path sandbox, AGENTS.md, .dntspace/skills/

   │

   ├── Models (global)   ──► providers, API keys, image/video tool models

   │

   └── Packs (global)    ──► skills + MCP + workflows (+ config/secrets) when enabled

```



- **One open profile = one window.** Open profiles restore on launch.

- **API keys and packs are app-wide.** Chat history, MCP *config*, skills disable list, vault secrets, and default agent mode are **per profile**.

- **Path sandbox:** file tools cannot leave the workspace root. `run_command` cwd is the workspace; absolute/`..` paths outside the root are denied (heuristic — not a full OS sandbox).



## Quick first session

1. Install from [Releases](https://github.com/dungnt1312/dntspace-releases/releases) (see [Getting started](./getting-started.md)).
2. Finish onboarding or open **Settings → Models** and add a provider + API key.
3. **Open Folder** for the current profile.
4. Pick an agent mode on the composer (**Ask before edit** is the safe default).
5. Send a message. Approve tool calls when prompted.
## Audience notes



- **End users:** follow the task-oriented pages above; skim [Settings reference](./settings-reference.md) when looking for a toggle.

- **Agents / automation:** start with [Agent guide](./agent-guide.md), then [Chat & agent modes](./chat-and-modes.md) and [Tools & approvals](./tools-and-approvals.md). Prefer reading workspace `AGENTS.md` and loading skills via `load_skill` / `$skill-name` rather than inventing process.

