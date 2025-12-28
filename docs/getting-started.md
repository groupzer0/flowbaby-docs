---
sidebar_position: 2
---

# Getting Started

This guide will help you install and configure Flowbaby for your VS Code workspace.

## Prerequisites

Before installing the extension, ensure you have:

- **VS Code** 1.106.0 or higher
- **Python** 3.10–3.12 installed on your system
- **Microsoft Visual C++ Redistributable** (Windows only) - [Download here](https://aka.ms/vs/17/release/vc_redist.x64.exe)

Flowbaby automatically creates and manages its own isolated Python environment (`.flowbaby/venv`) in each workspace. You do not need to install any Python packages manually.

## Installation

### Install from VS Code Marketplace

1. Open VS Code
2. Go to the Extensions view (`Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Search for **"Flowbaby"**
4. Click **Install**
5. Reload VS Code when prompted

## Setup

After installation, configure your workspace:

> Optional but recommended: Add `.flowbaby/` to your workspace `.gitignore` if you don't want Flowbaby's local data and virtual environment committed to your repository. You can always keep it versioned if you prefer.

### 1. Initialize Workspace (Automated)

Flowbaby now manages its own Python environment automatically.

1. Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"Flowbaby: Initialize Workspace"**
3. The extension will:
   - Check for Python 3.10-3.12
   - Create a dedicated `.flowbaby/venv` in your workspace (isolated from project venvs)
   - Install `cognee` and dependencies
   - Verify the environment is ready

### 2. Configure API Key

Flowbaby needs an **LLM provider API key** (OpenAI, Anthropic, etc.) for memory operations:

| What the API key does | What it does NOT do |
|----------------------|--------------------|
| ✅ Embed memories into searchable vectors | ❌ No telemetry or analytics |
| ✅ Retrieve relevant context via semantic search | ❌ No code analysis |
| ✅ Summarize conversations for long-term storage | ❌ Never transmitted except to your LLM provider |

> 💰 **Cost**: With default settings (OpenAI gpt-4o-mini), typical usage costs **$0.02–$0.06 per day** (~2-6 US cents). Your experience may vary. You can change providers/models in Settings → Flowbaby.

**Set your API key (stored securely via VS Code SecretStorage):**

1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Run **"Flowbaby: Set API Key"**
3. Enter your LLM provider API key when prompted

This key is stored securely and applies to all workspaces automatically.

### 3. Verify Extension Activation

1. Check the status bar for **"Flowbaby"** (checkmark)
2. If you see **"Flowbaby: Setup Required"** (yellow warning), click it to run setup
3. Optional: Open the Output panel (**View → Output**) and select **"Flowbaby"** to see logs

## Windows Troubleshooting

### Refresh dependencies fails with EPERM (rename `.flowbaby\\venv`)

If **"Flowbaby: Refresh Bridge Dependencies"** fails with an `EPERM` rename error on Windows, a Python process is usually still running and holding a lock inside `.flowbaby\\venv` (often the bridge daemon).

- Reload the window (**Developer: Reload Window**) and retry
- Close VS Code fully (all windows), wait a moment, then retry
- As a last resort, stop the `python.exe` process that references `.flowbaby\\venv` in its command line

## Memory-Aware Copilot Instructions (Strong Defaults)

> ⚠️ **Important**: Without explicit instructions, Copilot may not use memory tools in the way you desire. Adding a memory contract to your workspace ensures Copilot proactively stores and retrieves memories.
>
> 📄 **Quick option**: Copy the [memory contract example](https://github.com/groupzer0/vs-code-agents/blob/main/vs-code-agents/reference/memory-contract-example.md) to your workspace as `.copilot-instructions.md`

Flowbaby already nudges GitHub Copilot to store and retrieve memory when it makes sense. However, strong Copilot instructions are important if you want Copilot-initiated memory storage and retrieval to match your workflow (what to remember, what to ignore, how aggressive to be). The template below is a recommended starting point you can customize.

Create or modify an `.agent.md`, `.chatmode.md` or `.copilot-instructions.md` file in your workspace and add something like the example below, or use it exactly as-is.

**Example Copilot instructions:**

```markdown
---
name: Memory-Aware Code Assistant
description: Copilot assistant with access to workspace memory
tools: ['search', 'flowbabyStoreSummary', 'flowbabyRetrieveMemory']
---

You are a code assistant with access to workspace-specific memory powered by Flowbaby.

# 1. Retrieval (start of turn)

Treat the user’s current request and open documents as the primary source of truth. Use Flowbaby memory to augment and cross-check, not to override active specs.

At the start of any turn where past work might matter (prior plans, decisions, constraints, patterns):

1. Call #flowbabyRetrieveMemory **before** deep planning or multi-step reasoning.
2. Use a natural-language query that:
   - Describes the current task, question, or challenge
   - Mentions the area of the codebase or system involved
   - States what you are looking for (e.g., prior decisions, constraints, risks, patterns, open questions)
3. Prefer a small set of high-value memories (default: 3) rather than many low-signal items.

You MAY make at most one follow-up retrieval in the same turn, but only if:

- The first call returned nothing useful and a slightly more general query is warranted, or
- You have a clear new question (e.g., "Have we already decided how to handle this exact edge case?").

Do NOT chain multiple retrievals just to explore history. If more context seems useful, summarize what you know, note uncertainties, and say what you would ask the user for.

# 2. Using Retrieved Memory

When memory is available:

- Use it to reveal historical decisions, constraints, and tradeoffs.
- Check for prior attempts and repeated failures before proposing new work.
- Call out when current plans might conflict with older decisions.

If memory conflicts with current instructions or docs:

- Treat current instructions, specs, and architecture docs as the source of truth.
- Treat memory as historical context unless the user explicitly says otherwise.
- Briefly surface only material conflicts that would change risk, scope, or recommendations.

# 3. Summarization (end of work / milestones)

Use #flowbabyStoreSummary to maintain accurate long-term memory. Store a summary when:

- You complete meaningful work or a plan milestone
- You make or refine important decisions
- You discover new constraints, risks, or assumptions
- A conversation branches into a new line of work

Each summary should be 300–1500 characters and semantically dense. Capture:

- Goal or question
- Key findings and decisions
- Important reasoning and tradeoffs
- Rejected options and why they were rejected
- Notable constraints, risks, and assumptions
- Current status (ongoing, blocked, or complete)

Use fields like:

- `topic`: short 3–7 word title
- `context`: rich narrative summary
- `decisions`: list of important decisions
- `rationale`: reasons and tradeoffs
- `metadata.status`: e.g., `Active`, `Superseded`, or `DecisionRecord`

After storing, explicitly tell the user that you saved progress to Flowbaby memory.

# 4. Behavioral Requirements

- Begin each turn by asking: "Could prior work matter here?" If yes, retrieve.
- Never let memory silently override current specs, plans, or architecture.
- Reference memory explicitly when it shapes your recommendations.
- Avoid retrieval rabbit holes: at most one follow-up retrieval per turn.
- Regularly create summaries so future work can build on stable, well-structured context instead of raw chat logs.
- When multiple options are discussed, record both chosen and rejected paths (with rationale) in summaries.
```
