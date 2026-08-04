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



```Day 1 (Jul 23, evening) – Arrival, Canmore

Stroll Canmore Pedestrian Bridge
Dinner: Communitea Cafe (vegan/vegetarian bowls) or if craving Indian, Mumbai Local Indian Cuisine (butter chicken, lamb biryani, very authentic)

Day 2 (Jul 24) – Canmore exploration

Coffee/breakfast: Eclipse Coffee Roasters (rose cardamom latte, breakfast wraps) or Rocky Mountain Bagel Company (two locations — fresh bagels, lox, matcha)
Hike/bike at Canmore Nordic Centre, swim/picnic at Quarry Lake Park
Lunch option: Canmore Shawarma (Mediterranean-style wraps, falafel, halal)
Dinner: One98eight, or for Indian try The Roaring Rolls (Indian fusion, kadai paneer) or Saffron Canmore (biryani & chaat, great no-onion-no-garlic Jain options)

Day 3 (Jul 25) – Banff townsite & hot springs

Coffee: Mountain Folk Coffee Co. or Analog Banff (both excellent espresso, cozy vibe)
Banff Gondola, Cave and Basin, Bow Falls, Pedestrian Bridge
Lunch: The Balkan Mediterranean Restaurant (Greek/Mediterranean, halal-friendly, famous for lamb Arni)
Dinner: Zyka Elevated Indian Restaurant (top-rated, Kerala biryani, dosa) or Saffron Indian Bistro / Indian Curry House

Day 4 (Jul 26) – Lake Louise & Moraine Lake

Grab bagels to-go from Rocky Mountain Bagel Company before the early start
Lake Louise lakeshore, Lake Agnes Tea House hike, Moraine Lake shuttle + Rockpile Trail

Day 5 (Jul 27) – Bow Valley Parkway

Johnston Canyon, Lake Minnewanka, Cascade of Time Garden
Dinner: Farm & Fire or Nourish Bistro

Day 6 (Jul 28) – Free morning, Banff

Coffee & pastry: Wild Flour Bakery or Whitebark Cafe
The Whyte Museum (optional)
Farewell dinner: Good Folk, or if you haven't had your Mediterranean fix, The Balkan again

Day 7 (Jul 29) – Drive back to Calgary

A few standouts worth prioritizing given your interests:

Zyka and Mumbai Local are consistently the highest-rated Indian spots in the area
The Balkan is the go-to Mediterranean/Greek spot in Banff (book ahead, gets busy)
Rocky Mountain Bagel Company has two Canmore locations and is a local institution — great for grab-and-go before hikes
Analog and Eclipse Coffee Roasters are the top-rated specialty coffee spots```
