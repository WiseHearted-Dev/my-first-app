# Standard Operating Procedure (SOP): Local AI-Assisted Development Environment

## Objective
Maintain a stable, local-first AI coding environment on an Intel i7 MacBook Pro (16GB RAM) using VS Code, Ollama, and Aider, avoiding the JSON parsing/tool-calling crashes common with GUI extensions.

## 🛠️ Overview & Architecture

This repository records the configuration and workflows used to establish a privacy-focused, zero-cost, local AI coding assistant integrated directly into VS Code.

## System Requirements & Architecture
- **Hardware**: Intel i7 MacBook Pro, 16GB RAM
- **Code Editor**: Visual Studio Code
- **Local LLM Engine**: Ollama (qwen2.5-coder:7b)
- **AI Coding Agent**: Aider (CLI running via VS Code Integrated Terminal)
- **Version Control**: Git & GitHub

## Component Roles
| Component | Function |
|-----------|----------|
| Ollama    | Hosts and serves the local qwen2.5-coder:7b model via an HTTP API. |
| Qwen2.5-Coder:7B | State-of-the-art open coding model optimized for code understanding, generation, and multi-file reasoning. |
| Aider     | Translates natural language prompts into code changes using plain-text diffs instead of brittle JSON tool schemas. |
| VS Code   | Serves as the primary workspace, text editor, terminal host, and Git manager. |
| GitHub    | Stores cloud backups and tracks commit history. |

## Initial Setup Instructions

### Prerequisites
1. Verify Python 3.10+:
    ```bash
    python3 --version
    ```
2. Verify Git:
    ```bash
    git --version
    ```

### Step 1: Install & Verify Ollama
1. Install Ollama:
    ```bash
    curl -fsSL https://ollama.com/install.sh | sh
    ```

2. Start Ollama and pull the target coding model:
    ```bash
    # Start Ollama service (if running in background/headless)
    ollama serve

    # Pull Qwen2.5-Coder model:
    ollama pull qwen2.5-coder:7b
    ```
3. Verify the model is available:
    ```bash
    ollama list
    ```

### Step 2: Install Aider
Run the installation using Python 3:
    ```bash
    python3 -m pip install aider-chat
    ```

## Daily Operational Workflow

### Step 1: Launch Local Services
1. Open VS Code to your target project folder.
2. Open the built-in terminal (Control + ~).
3. Ensure Ollama is active in the background.

### Step 2: Start Aider Session
In the VS Code terminal, execute:
    ```bash
    python3 -m aider --model ollama/qwen2.5-coder:7b
    ```
**First Run Note**: If prompted with "Add .aider* to .gitignore (recommended)?", select Yes (y).

### Step 3: Prompting & Code Generation
1. Add files to context:
    ```plaintext
    /add index.html
    ```
2. Submit a task:
    - Write a dark-theme HTML page with a styled action button.
3. Verify changes: Aider streams text-diff edits directly to the file, saving changes automatically.

## Verification & Version Control Workflow

### Step 1: Test Locally in Browser
Prerequisite before pushing.
Open the file in Finder and double-click `index.html` (or use the VS Code Live Server extension) to test functionality in the browser.
Verification: Confirm the browser renders layout changes as expected.

### Step 2: Handle Untracked Config Files
VS Code Source Control.
Check the Source Control tab in VS Code. If non-code files (such as `.gitignore`) show under Changes, enter a commit message (e.g., "Add .gitignore") and click Commit.
Verification: Confirm the Changes list is clear.

### Step 3: Sync to GitHub: Cloud Backup
Click Sync Changes in the VS Code status bar to push local commits to `origin/main` on GitHub.
Verification: Confirm the status bar indicates local and remote branches are in sync.

## Essential Aider Slash Commands
- `/add <filepath>` — Add a file to Aider's context.
- `/drop <filepath>` — Remove a file from Aider's context.
- `/undo` — Revert the last AI code edit and roll back its Git commit.
- `/diff` — View exact line changes made in the last edit.
- `/tokens` — Check active context window usage.
- `/exit` — End the Aider CLI session.

## Troubleshooting & Safety Warnings
Avoid GUI Extensions Using Native JSON Tool-Calling
GUI extensions (such as ZooCode or Roo Code) relying on native JSON function-calling schema layers will trigger Model Response Incomplete or switch_mode infinite loops on 7B models. Always run Aider via the terminal for local models, as it relies strictly on plain-text search-and-replace blocks.

- **Command Not Found Error**: If aider fails to run directly, invoke it using `python3 -m aider`.
- **Fixing Bad Code**: If a generated result breaks the app, type `/undo` in the Aider prompt immediately to revert to the previous working Git state.
- **Aider hangs/stalls when querying `ollama/qwen2.5-coder:7b`**: 
  - **Root Cause:** Large context window payloads (repository maps and large file additions) exceed local VRAM/RAM allocation, or unhandled Ollama server timeout during cold start.
  - **Resolution Protocol:**
    - 1. **Interrupt Hanging Execution:** Press `Ctrl+C` to break out of the hanging generation process and restore the prompt loop.
    - 2. **Verify Server Health:**
      ```bash
      ollama list
      ```
    - 3. **Optimize Edit Format & Context Size:** When running models under 14B parameters, using the `--edit-format whole` flag reduces parsing overhead and improves model adherence:
      ```bash
      OLLAMA_API_BASE=http://localhost:11434 aider \
        --model ollama/qwen2.5-coder:7b \
        --edit-format whole
      ```

## 💡 Engineering Key Takeaways & Skills Demonstrated

* **Local AI Infrastructure:** Built a fully offline, privacy-compliant AI development workflow.
* **Prompt Engineering & Context Management:** Managed context windows via Aider's repository mapping, `/ask` commands, and `/read` modes.
* **System Troubleshooting:** Diagnosed local API timeout bottlenecks, VRAM constraints, and execution interrupts (`Ctrl+C` lifecycle).