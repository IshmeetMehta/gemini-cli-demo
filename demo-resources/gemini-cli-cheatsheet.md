# Gemini CLI Cheatsheet

### General Commands
| Command | Syntax | Description |
| :--- | :--- | :--- |
| **Start REPL** | `gemini` | Opens the interactive shell. |
| **Query** | `gemini -p "prompt"` | Executes a non-interactive query. |
| **Interactive Query** | `gemini "prompt"` | Executes a query and stays in the interactive session. |
| **Piped Input** | `cat file \| gemini` | Processes content from stdin. |
| **Update** | `gemini update` | Updates the CLI to the latest version. |

### Project Management
| Command | Syntax | Description |
| :--- | :--- | :--- |
| **Extensions** | `gemini extensions <cmd>` | Manage extensions (install, list, update, uninstall). |
| **MCP Servers** | `gemini mcp <cmd>` | Configure Model Context Protocol servers. |
| **Skills** | `gemini skills <cmd>` | Manage agent skills (list, install, enable/disable). |
| **Worktree** | `gemini -w [name]` | Start Gemini in a new git worktree (experimental). |
| **Sandbox** | `gemini -s` | Run in a sandboxed environment for safer execution. |

### Session Control
| Command | Syntax | Description |
| :--- | :--- | :--- |
| **Resume Latest** | `gemini -r "latest"` | Continues the most recent session. |
| **Resume by ID** | `gemini -r "<id>"` | Resumes a specific session by ID or index. |
| **List Sessions** | `gemini --list-sessions` | Displays available sessions for the current project. |
| **Delete Session** | `gemini --delete-session <#> ` | Deletes a session by its index number. |

### Configuration
| Option | Alias | Description |
| :--- | :--- | :--- |
| **Model** | `-m <model>` | Specify model (e.g., `auto`, `pro`, `flash`, `flash-lite`). |
| **Approval Mode** | `--approval-mode` | Set tool security (choices: `default`, `auto_edit`, `yolo`, `plan`). |
| **Output Format** | `-o <format>` | Set output style (`text`, `json`, `stream-json`). |
| **Include Dir** | `--include-directories` | Add additional directories to the workspace context. |

### Shortcuts (Interactive REPL)
| Command | Description |
| :--- | :--- |
| `/help` | Show help for all interactive commands. |
| `/quit` | Exit the interactive session. |
| `/memory reload` | Reload context files (e.g., `GEMINI.md`). |
| `/skills reload` | Reload discovered skills from disk. |
| `/mcp reload` | Restart and reload all MCP servers. |
| `/commands reload` | Reload custom slash commands. |
