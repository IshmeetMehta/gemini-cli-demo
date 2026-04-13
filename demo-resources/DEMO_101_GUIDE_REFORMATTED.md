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
*   **Setup:** `Create a .env file with a dummy API secret.` Then manually create a `.geminiignore` file in the root containing `.env`. Use file in the examples folder.
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

> **Note:** Gemini CLI automatically discovers skills in the `.gemini/skills` directory. You can also use `.agents/skills` as a more generic alternative.

*   **Prompts:**
    1.  `/skills list` (Verify `api-auditor` is discovered).
    2.  **Prompt:** `Can you audit http://geminicli.com`
*   **Observe:** The CLI should trigger the skill, run the local Node script, and output the result.

### Safe Shell Execution & Auto-Debugging
*   **Setup:** `Create a package.json with a script called "build" that runs tsc main.ts. Next, create a main.ts file with a function that adds two numbers, but deliberately introduce a TypeScript type error (e.g., assign a string to a variable explicitly typed as a number).`
*   **Observe:** The CLI executes the terminal command, reads stderr, fixes the file, and re-executes.

#### Step 1: Initialize the Project
Open your standard terminal, create a new directory for the test, and initialize a barebones Node.js project. You will also need to install TypeScript so the compiler is available to the CLI.

```bash
mkdir cli-debug-test
cd cli-debug-test
npm init -y
npm install typescript --save-dev
npx tsc --init
```

#### Step 2: Configure the Build Script
Open the newly generated `package.json` file. You need to add the specific build command you want the CLI to execute. Modify the scripts section to look like this:

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
Create a file named `main.ts` in the root of your project directory. Add a simple function, but explicitly declare a variable as a number and assign a string to it to trigger a compilation failure.

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
Start the Gemini CLI inside this project directory. Feed it the exact prompt you designed.
*   **Prompt:** `Run the npm run build command. Since it will fail, capture the error output, fix the offending code in main.ts, and automatically re-run the build command to verify your fix.`

#### Step 5: Validate the Execution Loop
To pass the test successfully, the CLI must perform the following sequence of actions without requiring additional human prompts:
1.  Execute `npm run build` in the shell.
2.  Fail the build and read the resulting `TS2322: Type 'string' is not assignable to type 'number'` error from standard error (stderr).
3.  Access and rewrite `main.ts` to fix the type mismatch (for example, by changing `"I am a string, not a number"` to `5`).
4.  Re-execute `npm run build`.
5.  Terminate the loop and report success to you once the command exits with a clean status code.

*   **Observe:** The CLI executes the terminal command, reads stderr, fixes the file, and re-executes.

---

## Phase 4: Memory, Context & Session Management
**Goal:** Control what Gemini knows about you and manage your persistent interaction history.

### 1. Project-Wide Rules (`GEMINI.md`)
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

### 2. Hierarchical Context (`GEMINI.md`)
*   **Setup:** Create two files manually.
    *   `./GEMINI.md` (Root): `We use React with Vite. Use Tailwind CSS for all styling. Tone: Be exceptionally enthusiastic.`
    *   `./src/GEMINI.md` (Nested): `All new components must include a Vitest unit test.`
*   **Prompt:** `Create a new button component in ./src/components/.`
*   **Observe:** The agent's response should be highly enthusiastic, use React/Tailwind (from root), AND generate a Vitest test (from the `src/` rule).

### 3. Hierarchical Context Test Setup
This section provides the step-by-step walkthrough to test if the CLI correctly merges and applies instructions from multiple `GEMINI.md` files located at different levels of the directory tree.

#### Step 1: Initialize the Project Structure
Open your terminal and create a new directory for this test, along with the nested `src` directory where our component will live.

```bash
mkdir cli-hierarchical-test
cd cli-hierarchical-test
mkdir -p src/components
```

#### Step 2: Create the Root Context File
Create a file named `GEMINI.md` in the root of your project directory (`./GEMINI.md`). This establishes the global rules for the entire project.

Add the following content:

```markdown
# Global Instructions
- We use React with Vite.
- Use Tailwind CSS for all styling.
- Tone: Be exceptionally enthusiastic!
```

#### Step 3: Create the Nested Context File
Create a second `GEMINI.md` file inside the `src` directory (`./src/GEMINI.md`). This adds specific rules that should only apply when the CLI operates inside the `src` folder.

Add the following content:

```markdown
# Frontend Instructions
- All new components must include a Vitest unit test.
```

#### Step 4: Run the Gemini CLI Prompt
Start the Gemini CLI inside the root directory of this project. Execute the following prompt to create a component inside the `src` hierarchy:
*   **Prompt:** `Create a new button component in ./src/components/.`

#### Step 5: Validate the Output
To pass this test, the CLI must successfully traverse the directory tree, combine the contexts from both `GEMINI.md` files, and apply all constraints. Verify the generated output against these criteria:
*   **Framework (Root Context):** The code should be a functional React component (likely `Button.jsx` or `Button.tsx`).
*   **Styling (Root Context):** The component must use Tailwind CSS utility classes (e.g., `className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"`).
*   **Tone (Root Context):** The CLI's conversational response should be clearly and exceptionally enthusiastic (e.g., "I would absolutely LOVE to build that button for you!").
*   **Testing (Nested Context):** The CLI must generate a corresponding test file (e.g., `Button.test.jsx` or `Button.test.tsx`) that explicitly uses Vitest (`import { describe, it, expect } from 'vitest'`).

### 4. Conversational Memory (Saving Facts)
*   **Prompts (Run sequentially):**
    1.  `Remember that I prefer using 'const' over 'let' wherever possible.`
    2.  `Save the fact that the staging server IP is 10.0.0.5.`
    3.  `Write a script to deploy to staging.`
*   **Observe:** `SaveMemory` in `~/.gemini/memory/demo-resources/GEMINI.md`.
*   **Observe:** The generated deployment script should automatically target `10.0.0.5`.
*   **Verification Prompt:** `/memory show` (Verify the saved facts are present in the loaded context).

### 5. Session Management (Resume, List, Delete)
*   **Setup:** Open a terminal, type `gemini`, prompt `Hello, this is Session A`, then type `/quit`. Open again, prompt `Hello, this is Session B`, then `/quit`.
*   **Tests (Run from your standard terminal):**
    1.  `gemini --list-sessions` (Observe: Lists your recent sessions).
    2.  `gemini -r` (Observe: Automatically resumes your most recent session, "Session B").
    3.  Inside the session, type `/resume`. (Observe: Opens the interactive browser. Use arrow keys to explore, press `x` to delete an old session).
    4.  Type `/quit` to exit, then run `gemini --delete-session 1` to test command-line deletion.

---

## Phase 5: Web Search and Fetch
This document provides the step-by-step walkthrough to test the CLI's ability to search the web, fetch specific URLs for deep context, and apply external documentation to local code.

*   **Goal:** Test the ability to pull external, up-to-date documentation and apply it locally.
*   **Tests (Run sequentially):**
    *   **Search (Finding Documentation):** `Search for the 'Bun 1.0' release notes and summarize the key changes.`
        *(Observe: The CLI uses the `google_web_search` tool to ground the response in live internet data).*
    *   **Fetch (Deep Context from URL):**
        `Read https://geminicli.com/docs/cli/tutorials/web-tools/ and explain how to apply it to my code.`
        *(Observe: The CLI uses the `web_fetch` tool to retrieve the raw page content, stripping ads/nav).*
    *   **Apply Knowledge to Code (Workflow):**
        `Search: "How do I implement auth with Supabase?"` ->
        `Fetch: "Read this guide: https://supabase.com/docs/guides/auth"` ->
        `Implement: "Great. Now use that pattern to create an auth.ts file in my project."`
    *   **Troubleshoot Errors:**
        `I'm getting 'Error: hydration mismatch' in Next.js. Search for recent solutions.`
        *(Observe: The CLI searches GitHub issues, StackOverflow, or forums for recent fixes).*

### Step 1: Initialize the Test Environment
While some of these tests are conversational, the third test requires modifying a local project. Let's create a scratchpad directory for this.

```bash
mkdir cli-web-tools-test
cd cli-web-tools-test
npm init -y
```

### Step 2: Test Web Search (Finding Documentation)
Start the Gemini CLI in your terminal and run the following prompt to test its live internet access.
*   **Prompt:** `Search for the 'Bun 1.0' release notes and summarize the key changes.`

### Step 3: Test URL Fetching (Deep Context)
Next, test the CLI's ability to read raw page content from a specific URL, stripping away unnecessary HTML/navigation bloat.
*   **Prompt:** `Read https://geminicli.com/docs/cli/tutorials/web-tools/ and explain how to apply it to my code.`

**Validation Criteria:**
*   **Tool Usage:** The CLI should trigger a fetch/read tool (e.g., `web_fetch`).
*   **Comprehension:** The response must directly reference the specific content found on that page and provide actionable advice on how to integrate those concepts locally.

### Step 4: Test Workflow (Apply Knowledge to Code)
This test evaluates the CLI's ability to chain tasks: Search -> Fetch -> Implement. Run these prompts sequentially in the same chat session.
*   **Prompt 1 (Search):** `Search: "How do I implement auth with Supabase?"`
*   **Prompt 2 (Fetch):** `Read this guide: https://supabase.com/docs/guides/auth`
*   **Prompt 3 (Implement):** `Great. Now use that pattern to create an auth.ts file in my project.`

**Validation Criteria:**
*   **Continuity:** The CLI must remember the context from Prompts 1 and 2.
*   **Execution:** It should generate a new file named `auth.ts` in your current directory.
*   **Accuracy:** The code inside `auth.ts` must reflect the official Supabase patterns found in the fetched documentation (e.g., utilizing `@supabase/supabase-js`).

### Step 5: Test Error Troubleshooting
Finally, test the CLI's ability to act as a debugging assistant by looking up recent solutions to specific framework errors.
*   **Prompt:** `I'm getting 'Error: hydration mismatch' in Next.js. Search for recent solutions.`

**Validation Criteria:**
*   **Tool Usage:** The CLI should execute a web search.
*   **Relevance:** It should bring back current, standard fixes for Next.js hydration issues (e.g., using `useEffect` to delay rendering, checking for invalid HTML nesting, or using `suppressHydrationWarning`). It should clearly synthesize solutions from community sources like GitHub issues or Stack Overflow.

---

## Phase 6: Core Terminal Integration

### 1. Bash Script Automation
*   **Setup:** `Initialize an empty git repository here. Create a dummy package.json (version 1.0.0) and a Dockerfile. Commit them.`
*   **Prompt:** `Create a bash script named prep-deploy.sh that will: bump the patch version in package.json, build the Docker image tagging it with the new version, and output a success message. Make the script executable.`
*   **Observe:** The CLI generates valid bash, uses `chmod +x`, and chains the commands.

### 2. Terminal Integration Test Setup
This document provides the step-by-step plan to execute Phase 6: Core Terminal Integration tests. These tests validate the CLI's ability to manipulate the local file system, handle standard input streams, and bypass interactive prompts for automated workflows.

#### Step 1: Initialize the Environment & Git
Open your standard terminal and create a dedicated directory for these tests. This step includes the setup for the Bash Script Automation test.

```bash
mkdir cli-terminal-test
cd cli-terminal-test
git init

# Create a dummy package.json
echo '{ "name": "dummy-app", "version": "1.0.0" }' > package.json

# Create a dummy Dockerfile
echo 'FROM alpine:latest' > Dockerfile

# Commit the initial state
git add .
git commit -m "Initial commit for CLI testing"
```

#### Step 2: Test Bash Script Automation
Start the interactive Gemini CLI inside the `cli-terminal-test` directory and issue the command to generate the bash script.
*   **Prompt:** `Create a bash script named prep-deploy.sh that will: bump the patch version in package.json, build the Docker image tagging it with the new version, and output a success message. Make the script executable.`

**Validation:**
1.  Check that `prep-deploy.sh` was created in the directory.
2.  Verify its execution permissions: run `ls -la` and look for the `x` (executable) flag on the script.
3.  Check the script contents. It should use commands like `npm version patch` (or `sed` to edit the file) and `docker build -t dummy-app:$(node -p "require('./package.json').version")`.

### 3. Non-Interactive Queries (Piping)
*   **Prompt:** Exit the interactive CLI session. In your standard terminal, run:
    `echo "def add(a, b): return a + b" | gemini -p "Add type hints and a docstring to this function."`
*   **Observe:** Processes standard input and returns the typed function without opening the chat interface.

### 4. Auto-Edit Approval Mode
*   **Prompt:** Open a new session with the auto-edit flag:
    `gemini --approval-mode auto_edit`
    Then ask: `Add a new comment at the top of main.py explaining that this is a test file.`
*   **Observe:** The CLI edits the file immediately without presenting a confirmation dialogue, optimizing for fast, git-diff-based workflows.
