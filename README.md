# Gemini CLI Demo: Step-by-Step

Welcome! This repository is designed to show you how **Gemini CLI** can assist in real-world software development. Follow these steps to experience the workflow.

## 1. Preparation
Ensure you have Gemini CLI installed and you've signed in:
```bash
npm install -g @google/gemini-cli
gemini
```

## 2. Explore the Project Structure
Open this project in your favorite editor (e.g., VS Code). You'll see:
- `application/`: The core FastAPI backend and React frontend.
- `demo-resources/`: Guides and cheatsheets to help you learn Gemini CLI commands.
- `examples/`: Simple snippets for quick experiments.

## 3. Running the Demo Walkthrough
For a structured tour of the tool's capabilities, we provide two main guides in the `demo-resources/` directory:

### [Gemini CLI 101: Core Blueprint](demo-resources/DEMO_101_GUIDE.md)
A comprehensive 12-phase guide covering:
- **Core Scaffolding:** Create and modify apps instantly.
- **Unit Testing:** Write and run tests automatically.
- **Multi-file Context:** Use the `@` syntax for cross-file refactoring.
- **Agent Skills:** Audit websites or run custom scripts.
- **Custom Commands:** Define reusable TOML workflows.
- **System Prompt Override:** Customize the agent's core persona via `system.md`.
- **Web Search/Fetch:** Pull live documentation into your code.
- **Task Planning:** Use Plan Mode to manage complex refactors.
- **MCP Servers:** Connect to external tools like GitHub via Docker.

### [Gemini CLI 201: Advanced Workflows](demo-resources/DEMO_201_GUIDE.md)
Advanced techniques for power users:
- **Checkpoints:** Save and fork conversations using `/resume`.
- **Advanced Rewind:** Total state rollback for both code and chat.
- **Dynamic Steering:** Guide the model mid-draft during planning.

## 4. Key Exercises to Try
Once you're comfortable, try these prompts with the CLI:
- **Research:** "List all the API endpoints in the `application/backend` folder."
- **Implement:** "Add a new `User` model to `application/backend/models.py` with `id`, `name`, and `email`."
- **Fix:** "Run the tests in `application/backend` and help me fix any failures you find."

## 5. Reference
Check out [gemini-cli-cheatsheet.md](demo-resources/gemini-cli-cheatsheet.md) for a quick list of available commands and shortcuts.
