
# Gemini CLI 101: Sequential Feature Testing Blueprint

**Goal:** Evaluate the capabilities, productivity tools, and seamless context-aware interactions of the Gemini CLI. This guide is designed to be executed sequentially. Run the **Setup** prompts first to scaffold the environment, followed by the **Test** prompts.

---

## Phase 1: Core Scaffolding & Code Editing
*Start in an empty directory.*

### 1. Interactive Scaffolding
*   **Prompt:** `Create a simple FastAPI 'Hello World' app. Generate a requirements.txt file containing fastapi and uvicorn, and a main.py file that initializes the app with a single GET route returning {"message": "Hello World"}.`
*   **Observe:** Verify both files are created and populated correctly without manual intervention.

### 2. Code Modification
*   **Prompt:** `Add a new Pydantic model for an 'Item' (with a string name and a float price). Then, add a POST endpoint to main.py that accepts this 'Item' and returns it.`
*   **Observe:** The CLI should parse `main.py`, import `BaseModel`, and seamlessly insert the new endpoint without breaking the existing GET route.

### 3. Write Unit Tests
*   **Prompt:** `Using @main.py as context, create a new file @test_main.py. Write pytest-based unit tests that: 1) Verify the GET route returns 200, 2) Verify the POST /items route correctly echoes the Item payload, and 3) Assert that sending a string instead of a float for 'price' returns a 422 error. Finally, create a venv, install pytest and httpx, and run the tests.`
*   **Observe:** The CLI should identify the need for `TestClient` or `ASGITransport`, generate the test file without altering the source code, and provide a terminal output showing the test pass/fail status.

### 4. Codebase Investigation
*   **Setup:** `Let's make our FastAPI app a bit more complex. Create a src/ directory and move main.py into it. Rename main.py to app.py. Inside app.py, import CORSMiddleware from FastAPI and apply it to the app instance, allowing all origins.`
*   **Prompt:** `Using your codebase investigation tools or sub-agents, find exactly where the FastAPI app is initialized and explain what middleware is currently being applied to it.`
*   **Observe:** The CLI should traverse the directory, locate `src/app.py`, read the contents, and report the applied `CORSMiddleware`.

---

## Phase 2: File Navigation & Security

### 1. Multi-file Context (`@` syntax)
*   **Setup:** `Create a dummy React project. Make a src/components directory and add a basic UserProfile.tsx. Create a src/types directory and add User.ts.`
*   **Prompt:** `@src/components/UserProfile.tsx @src/types/User.ts Refactor the component to use the updated User interface.`
*   **Observe:** The CLI directly injects these specific files to process the refactor.

### 2. Exploration & `.geminiignore`
*   **Setup:** `Create a .env file with a dummy API secret.` Then manually create a `.geminiignore` file in the root containing `.env`. (Or use a file from the examples folder).
*   **Exploration Prompt:** `Find the file that defines the UserProfile component.`
*   **Security Prompt:** `Scan my directory and list all files containing secrets or passwords.`
*   **Observe:** The CLI should locate the `UserProfile` correctly, but respect the `.geminiignore` file and refuse to read or report the contents of the `.env` file.

---

## Phase 3: Agent Skills & Shell Execution

### Custom Agent Skills
*   **Setup (Creating the Skill):**
    1.  Run: `mkdir -p .gemini/skills/api-auditor/scripts`
    2.  Copy `examples/skills/api-auditor-skills.md` as `.gemini/skills/api-auditor/SKILL.md`. This tells the agent when to use the skill and how to behave.
    3.  Copy `examples/skills/audit.js` as `.gemini/skills/api-auditor/scripts/audit.js`. This is the code the agent will run. Skills can bundle resources like scripts.
    4.  Restart the Gemini CLI.
*   **Note:** Gemini CLI automatically discovers skills in the `.gemini/skills` directory. You can also use `.agents/skills` as a more generic alternative.

*   **Prompts:**
    1.  `/skills list` (Verify `api-auditor` is discovered).
    2.  Prompt: `Can you audit http://geminicli.com`
*   **Observe:** The CLI should trigger the skill, run the local Node script, and output the result.

## Phase 4: Safe Shell Execution & Auto-Debugging
*   **Goal:** Test the CLI's ability to execute commands, detect failures, and self-correct.

#### Step 1: Initialize the Project
Open your terminal, create a new directory for the test, and initialize a barebones Node.js project.
```bash
mkdir cli-debug-test
cd cli-debug-test
npm init -y
npm install typescript --save-dev
npx tsc --init
```

#### Step 2: Configure the Build Script
Modify `package.json` to include a build command:
```json
{
  "name": "cli-debug-test",
  "version": "1.0.0",
  "scripts": {
    "build": "tsc main.ts"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  }
}
```

#### Step 3: Create the Broken TypeScript File
Create `main.ts` with a deliberate type error:
```typescript
function addNumbers(a: number, b: number): number {
    return a + b;
}

// Deliberate type error to trigger the CLI's debugging loop
let valueToConfirm: number = "I am a string, not a number";

// @ts-ignore - intentional error for testing purposes
console.log(addNumbers(10, valueToConfirm as any));
```

#### Step 4: Run the Gemini CLI Prompt
*   **Prompt:** `Run the npm run build command. Since it will fail, capture the error output, fix the offending code in main.ts, and automatically re-run the build command to verify your fix.`

#### Step 5: Validate the Execution Loop
To pass the test successfully, the CLI must:
1.  Execute `npm run build` in the shell.
2.  Fail the build and read the `TS2322` error.
3.  Rewrite `main.ts` to fix the type mismatch.
4.  Re-execute `npm run build`.
5.  Report success once the command exits with status 0.

---

---

## Phase 5: Custom Commands
**Goal:** Define reusable model instructions and shell workflows using TOML configuration files.

### Step 1: Initialize the Commands Directory
Open your terminal and create the local commands directory.
```bash
mkdir -p .gemini/commands
```

### Step 2: Create a Custom Command
Create a file named `hello.toml` in the `.gemini/commands/` directory.
```toml
description = "A friendly greeting command"
prompt = "Say hello to {{args}} in an exceptionally enthusiastic tone!"
```

### Step 3: Run the Custom Command
Start the Gemini CLI and run your new command.
*   **Prompt:** `/hello World`
*   **Observe:** The CLI should recognize the custom command from the `.gemini/commands` folder, inject "World" into the `{{args}}` placeholder, and generate an enthusiastic response.

### Step 4: Context-Aware Shell Command
Create another command `grep-code.toml` to test shell injection.
```toml
description = "Search for a pattern in the codebase"
prompt = """
Please summarize the findings for the pattern `{{args}}`.
Search Results:
!{grep -r {{args}} .}
"""
```
*   **Prompt:** `/grep-code "FastAPI"`
*   **Observe:** The CLI should execute the `grep` command (safely escaping the argument), read the results back into the prompt, and summarize them for you.

---

## Phase 6: System Prompt Override
**Goal:** Completely replace the built-in system prompt with a project-specific persona or strict ruleset.

> [!IMPORTANT]
> This is a **full replacement**, not a merge. Built-in defaults will not apply unless you include them.

### Step 1: Create the System Prompt File
Create a file named `system.md` in your project's `.gemini/` directory.
```bash
mkdir -p .gemini
cat << 'EOF' > .gemini/system.md
# Persona: Minimalist Assistant
You are a minimalist coding assistant. Give only code snippets without any explanations, pleasantries, or formatting (unless specifically asked).
EOF
```

### Step 2: Enable the Override
Instruct the CLI to use this file by setting the `GEMINI_SYSTEM_MD` variable in your `.gemini/.env` file.
```bash
echo "GEMINI_SYSTEM_MD=true" >> .gemini/.env
```

### Step 3: Verify the New Persona
Start the Gemini CLI and ask for a simple script.
*   **Prompt:** `Write a python script to add two numbers.`
*   **Observe:** The CLI should show a `|⌐■_■|` indicator in the UI (signaling custom mode) and output *only* the code, following your minimalist rules.

---

## Phase 7: Memory, Context & Session Management
**Goal:** Control what Gemini knows about you and manage your persistent interaction history.

### Project-Wide Rules (`GEMINI.md`)
*   **Setup:** Create a file named `GEMINI.md` in the root:
    ```markdown
    # Project Instructions
    - **Environment:** Always create a Python 3 virtual environment (`python3 -m venv venv`) and use the source command (`source venv/bin/activate`) before using `pip3`.
    - **Framework:** We use FastAPI with Python 3.10+.
    - **Styling:** Prefer Annotated dependency injection.
    - **Testing:** All new logic must include a pytest unit test.
    - **Tone:** Be concise and professional.
    ```
*   **Prompt:** `Create a new auth service in auth_service.py.`
*   **Observe:** The agent should follow the guidelines (environment setup, style, testing) and maintain the specified tone.

### Hierarchical Context Test
This test checks if the CLI merges instructions from multiple `GEMINI.md` files.

#### Step 1: Initialize the Project Structure
```bash
mkdir cli-hierarchical-test
cd cli-hierarchical-test
mkdir -p src/components
```

#### Step 2: Create the Root Context File
Create `./GEMINI.md`:
```markdown
# Global Instructions
- We use React with Vite.
- Use Tailwind CSS for all styling.
- Tone: Be exceptionally enthusiastic!
```

#### Step 3: Create the Nested Context File
Create `./src/GEMINI.md`:
```markdown
# Frontend Instructions
- All new components must include a Vitest unit test.
```

#### Step 4: Run the Gemini CLI Prompt
*   **Prompt:** `Create a new button component in ./src/components/.`

#### Step 5: Validate the Output
1.  **Framework:** Is it a React component?
2.  **Styling:** Does it use Tailwind CSS?
3.  **Tone:** Is the response exceptionally enthusiastic?
4.  **Testing:** Is there a corresponding Vitest test file?

### Conversational Memory (Saving Facts)
*   **Prompts (Sequential):**
    1.  `Remember that I prefer using 'const' over 'let' wherever possible.`
    2.  `Save the fact that the staging server IP is 10.0.0.5.`
    3.  `Write a script to deploy to staging.`
*   **Observe:** Facts are saved to `~/.gemini/memory/.../GEMINI.md`.
*   **Observe:** The deployment script should target `10.0.0.5`.
*   **Verification:** Run `/memory show` to verify saved facts.

### Session Management
*   **Setup:** Create Session A and Session B by starting the CLI, prompting, and quitting.
*   **Tests:**
    1.  `gemini --list-sessions` (Lists recent sessions).
    2.  `gemini -r` (Resumes the last session).
    3.  `/resume` (Opens interactive session browser).
    4.  `/quit` and `gemini --delete-session 1` (Test deletion).

---

## Phase 8: Web Search and Fetch
**Goal:** Pull external, up-to-date documentation and apply it locally.

### Step 1: Initialize the Test Environment
```bash
mkdir cli-web-tools-test
cd cli-web-tools-test
npm init -y
```

### Step 2: Test Web Search
*   **Prompt:** `Search for the 'Bun 1.0' release notes and summarize the key changes.`
*   **Observe:** Uses `google_web_search` for live data.

### Step 3: Test URL Fetching (Deep Context)
*   **Prompt:** `Read https://geminicli.com/docs/cli/tutorials/web-tools/ and explain how to apply it to my code.`
*   **Observe:** Uses `web_fetch` to retrieve and comprehend page content.

### Step 4: Test Workflow (Search -> Fetch -> Implement)
Run sequentially:
1.  `Search: "How do I implement auth with Supabase?"`
2.  `Fetch: "Read this guide: https://supabase.com/docs/guides/auth"`
3.  `Implement: "Great. Now use that pattern to create an auth.ts file in my project."`
*   **Validation:** Continuity (memory across prompts), Execution (file creation), and Accuracy (correct pattern usage).

### Step 5: Test Error Troubleshooting
*   **Prompt:** `I'm getting 'Error: hydration mismatch' in Next.js. Search for recent solutions.`
*   **Observe:** Searches GitHub/StackOverflow and synthesizes standard fixes.

---

## Phase 9: Core Terminal Integration

### Bash Script Automation
*   **Setup:** `Initialize an empty git repository here. Create a dummy package.json (version 1.0.0) and a Dockerfile. Commit them.`
*   **Prompt:** `Create a bash script named prep-deploy.sh that will: bump the patch version in package.json, build the Docker image tagging it with the new version, and output a success message. Make the script executable.`
*   **Observe:** Generates valid bash, uses `chmod +x`, and chains commands.

### Non-Interactive Queries (Piping)
*   **Command:**
    ```bash
    echo "def add(a, b): return a + b" | gemini -p "Add type hints and a docstring to this piped function."
    ```
*   **Observe:** CLI outputs formatted code directly to stdout and terminates without opening the chat UI.

### Auto-Edit Approval Mode
*   **Setup:** `echo "print('hello world')" > main.py`
*   **Action:** Launch with `gemini --approval-mode auto_edit`.
*   **Prompt:** `Add a new comment at the top of main.py explaining that this is a test file.`
*   **Observe:** File is modified instantly without "[Y/n]" prompts.

---

## Phase 10: Task Planning
**Goal:** Break down complex jobs and track progress.

### Step 1: Trigger Plan Mode
*   **Prompt:** `I want to set up a basic Node.js project, create a simple javascript math utility file, and then migrate it to TypeScript. Please make a plan first.`
*   **Observe:** CLI invokes `write_todos` and outputs a structured list.

### Step 2: Iterate the Plan
*   **Prompt:** `You forgot to add a step to write a test file for the math utility. Please add that step before the TypeScript migration.`
*   **Observe:** Todo list is updated dynamically.

### Step 3: Execute and Monitor
*   **Prompt:** `Looks good. Start with the first step.`
*   **Observe:** State tracking (`[IN_PROGRESS]`, `[DONE]`) and `Ctrl+T` to toggle the list view.

### Step 4: Handle Unexpected Changes
*   **Prompt:** `Actually, let's skip the TypeScript migration entirely. Just finish the JavaScript test file and we are done.`
*   **Observe:** Remaining tasks are cancelled/removed.

---

## Phase 11: Plan Mode & Model Steering (Experimental)
**Goal:** Interrupt and guide the agent in real-time.

### Step 1: Start Complex Task
*   **Prompt:** `/plan I want to implement a new notification service using Redis.`

### Step 2: Mid-Flight Steering
*   **Action (While spinning):** Type `Don't forget to check packages/common/queues for the existing Redis config.` and press Enter.
*   **Observe:** CLI acknowledges and incorporates the hint into its research turn.

### Step 3: Design Refinement
*   **Action (While drafting):** `Actually, let's use a Publisher/Subscriber pattern instead of a simple queue for this service.`
*   **Observe:** Agent stops drafting and restarts based on new design feedback.

---

## Phase 12: MCP Server Setup
**Goal:** Configure and interact with external MCP servers (e.g., GitHub).

### Step 1: Configuration
Create a local `.gemini/settings.json` file to instruct the CLI to spin up the GitHub MCP container.

```bash
cat << 'EOF' > .gemini/settings.json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server:latest"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
EOF
```

### Step 2: Verification
*   **Command:** `/mcp list`
*   **Observe:** Displays status `Connected`.

### Step 3: Execution (Read/Write)
*   **Prompt:** `List the open PRs in the google/gemini-cli repository.`
*   **Prompt:** `Create an issue in my test repository titled "Bug: Login fails" with the description "See logs".`
*   **Observe:** Uses specific MCP tools to interact with external data.

