# Gemini CLI 101: Sequential Feature Testing Blueprint

**Goal:** Evaluate the capabilities, productivity tools, and seamless context-aware interactions of the Gemini CLI. This guide is designed to be executed sequentially. Run the **Setup** prompts first to scaffold the environment, followed by the **Test** prompts.

---
## Phase 5: Advanced Workflows, Planning & Session Management

### 10. Task Planning & Todo Management
* **Setup (JavaScript Project Creation):**
    * **Prompt:** `Create a basic Node.js Express application in JavaScript. Generate a package.json, a utils.js with some basic math functions, and a server.js that imports utils.js and sets up a simple API route.`
* **Tests (Run sequentially):**
    1.  **Generate Plan:** `I want to migrate this project from JavaScript to TypeScript. Please make a plan first.`
        *(Observe: The CLI uses the write_todos tool to generate a structured task list).*
    2.  **Iterate on Plan:** `You forgot to add a step for installing @types/node and @types/express. Please update the plan.`
        *(Observe: The todo list dynamically updates with the new steps).*
    3.  **Execute & Monitor:** `Looks good. Start with the first step.`
        *(Observe: As the agent works, press `Ctrl+T` to toggle the live todo list view and monitor progress).*
    4.  **Handle Changes:** `Actually, let's skip the 'server.js' refactor for now. Mark it as cancelled and finish the rest.`
        *(Observe: The agent dynamically adjusts the plan, removes the step, and continues).*

### 12. Checkpoints & Forking Conversations
* **Setup:** Start a new session (`gemini`).
    * Prompt: `Write a basic recursive Fibonacci function in Python.`
* **Test (Branching Workflow):**
    1.  Save this exact state: `/resume save fib-baseline`
    2.  Try approach A: `Optimize this function using memoization.`
    3.  Now, let's fork! Type: `/resume resume fib-baseline`
    4.  Try approach B: `Actually, optimize this using an iterative loop instead.`
* **Observe:** The CLI reverts the context back to the basic recursive function and branches off into the iterative approach without losing or contaminating the original state.

### 13. Advanced Rewind (Undo Mistakes)
* **Setup:** Start a new session. Prompt: `Create a file called utils.js with a simple add function.`
* **Test:**
    1.  Prompt: `Change the function name to be ALL CAPS.`
    2.  Type `/rewind` (or press `Esc` twice).
* **Observe:** The CLI presents an undo menu. Choose **Rewind both** (conversation and files). Verify `utils.js` reverts to the lowercase function name and the chat history "forgets" the ALL CAPS request.

