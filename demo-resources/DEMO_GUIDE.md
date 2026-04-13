# Gemini CLI 101 Demo Guide (Python Edition)

This guide provides a structured walkthrough for demonstrating the core features of Gemini CLI, as outlined in the "Get started," "Use Gemini CLI," and "Configuration" documentation, specifically tailored for Python developers.

## 1. Get Started: Initial Setup
**Goal:** Show how to get up and running in minutes.

*   **Installation:**
    ```bash
    npm install -g @google/gemini-cli
    ```
*   **Authentication:**
    1.  Run `gemini` for the first time.
    2.  Select **1. Sign in with Google** from the authentication options.
    3.  Complete the browser-based login flow.
*   **First Interaction:**
    - Create a new, empty folder: `mkdir python-gemini-demo && cd python-gemini-demo`
    - Start a session: `gemini`
    - Prompt: "How can you help me set up a basic FastAPI project with a virtual environment in this directory?"

## 2. Use Gemini CLI: Core Features
**Goal:** Demonstrate productivity tools and seamless context-aware interaction.

*   **Interactive Session (Scaffolding):**
    - Prompt: "Create a simple FastAPI 'Hello World' app with a `requirements.txt` and a `main.py` file."
    - Observe: Gemini CLI automatically creates both files and populates them with initial code.
*   **File Writing & Editing:**
    - Prompt: "Add a new Pydantic model for an 'Item' and a POST endpoint to `main.py` that accepts it."
    - Observe: Gemini CLI uses the `replace` tool or `write_file` to modify the code.
*   **Non-Interactive Queries (Piping):**
    - In your terminal: `echo "def add(a, b): return a + b" | gemini -p "Add type hints and a docstring to this function."`
    - Goal: Show integration with standard Unix pipes for quick transformations.
*   **Codebase Investigation:**
    - Prompt: "Using your sub-agents, find where the FastAPI app is initialized and explain the middleware being used."
    - Observe: Gemini CLI uses the `codebase_investigator` to search and report back.
*   **Slash Commands:**
    - Inside a session, type `/help`.
    - Explore other commands: `/memory reload` to refresh context or `/quit` to exit.

## 3. Configuration & Context
**Goal:** Show how to customize Gemini CLI's behavior to match your workflow.

*   **GEMINI.md for Project-Specific Rules:**
    - Create a file named `GEMINI.md` in the project root.
    - Content:
        ```markdown
        Always follow these rules in this project:
        - Use Python 3.10+ features (like pipe operator for unions).
        - Use a professional but enthusiastic tone in your responses.
        - Prefer using Ruff for linting and formatting.
        - Add a brief 'How to run' section to any README you generate.
        ```
    - Prompt: "Generate a README.md for this project."
    - Goal: Show how persistent context files shape Gemini's output without repeated instructions.
*   **Approval Modes:**
    - Mention the difference between the default prompt-based interaction and `--approval-mode auto_edit`.
    - Tip: Use `auto_edit` for faster, trust-based workflows where you can review changes via Git diff.
*   **Settings Exploration:**
    - Explain that user preferences like `vim_mode` or the default `model` can be toggled in `~/.gemini/settings.json`.

## 4. Advanced File Management & Refactoring
**Goal:** Showcase how Gemini CLI handles complex, multi-file tasks with precision and control.

*   **Context Discovery (Finding & Reading Files):**
    - **Scenario:** Refactoring the API layer across the stack.
    - **Prompt:** "Find all files related to the API and task management in both the backend and frontend directories. Summarize how they interact."
    - **Observe:** Gemini CLI uses `glob` and `grep_search` to locate `backend/main.py`, `backend/models.py`, and `frontend/src/api.ts`.

*   **Multi-File Refactoring (Modifying & Creating Files):**
    - **Scenario:** Improving REST compliance by changing status codes.
    - **Prompt:** "Update the `POST /tasks` endpoint in `backend/main.py` to return a `201 Created` status code. Then, create a new utility file `frontend/src/utils.ts` with a status checker function and use it in `api.ts`."
    - **Observe:** Gemini CLI performs surgical `replace` calls on existing files and uses `write_file` for the new one.

*   **Validation & Testing (Running Unit Tests):**
    - **Scenario:** Verifying the fix and closing the feedback loop.
    - **Prompt:** "Run the backend unit tests in `backend/test_main.py`. If they fail, diagnose the issue and apply a fix."
    - **Observe:** Gemini CLI runs `pytest` via `run_shell_command`, identifies a mismatch in expected status codes, and corrects the test or the implementation.

*   **Advanced Visibility Control (.geminiignore):**
    - **Scenario:** Hiding large binary or sensitive files from Gemini's context.
    - **Action:** Create a `.geminiignore` file in the root:
        ```text
        *.docx
        temp/
        ```
    - **Prompt:** "Analyze the root directory. Do you see any documentation files?"
    - **Observe:** Gemini CLI will ignore the `.docx` file, demonstrating how to maintain a clean and secure context.

*   **Review & Confirmation:**
    - Demonstrate how the CLI presents interactive diffs for every change, ensuring you maintain full control over the codebase before any write operation is finalized.

---
*Generated by Gemini CLI for demonstration purposes.*
