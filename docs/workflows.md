---
sidebar_position: 4
---

# Workflows

Learn effective patterns for using Flowbaby in your daily development.

## Usage

### 1. Context-Aware Automatic Memory Search

Flowbaby can automatically search your workspace memory and surface relevant context into GitHub Copilot chats.

**How it works**:
- Flowbaby watches for conversation cues (e.g., "How did I implement…?", "What did we decide about…?").
- When it detects a good opportunity, it runs a background search against your knowledge graph.
- Retrieved memories are added to the Copilot request as additional context so you don't have to restate prior work.

**What you'll see**:
- In Copilot Chat, Flowbaby shows a section like **"📚 Retrieved memories"** with short previews.
- Copilot's answer incorporates those memories directly into its response.

### 2. Context-Aware Automatic Memory Storage

Flowbaby can also decide when to summarize and store parts of the conversation as long-term memory.

**How it works**:
- Flowbaby looks for moments where you've reached a decision, finished a debugging session, or explained an important concept.
- It generates a structured summary and stores it in your workspace knowledge graph (after any required confirmation, depending on settings).

**Benefits**:
- You build up high-value memory over time with minimal manual effort.
- Later queries can retrieve these summaries instead of raw, noisy chat logs.

### 3. Using the @flowbaby Chat Participant

The `@flowbaby` participant lets you directly query your workspace memory.

**How to use**:
1. Open GitHub Copilot Chat (`Ctrl+Alt+I` or click the chat icon).
2. Address Flowbaby explicitly: `@flowbaby How did I implement caching?`
3. Flowbaby:
   - Retrieves relevant memories from your knowledge graph.
   - Shows previews under **"📚 Retrieved memories"**.
   - Augments your question with that context.
   - Generates a context-aware response.

**Example queries**:
- `@flowbaby What problems did we discuss about the authentication system?`
- `@flowbaby What solutions did we consider for rate limiting?`
- `@flowbaby Summarize our decisions about database architecture`

### 4. Keyboard Shortcut and Command Palette Capture

Use these flows when you want to explicitly capture important information.

**Keyboard shortcut**:
1. View a valuable chat message (from any participant: @workspace, @terminal, GitHub Copilot, etc.).
2. Press **Ctrl+Alt+F** (or **Cmd+Alt+F** on macOS).
3. Paste or confirm the content in the capture input.
4. Press Enter to capture.
5. You'll see a staged message (e.g., "Memory staged - processing will finish in ~1–2 minutes").

**Command Palette**:
1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`).
2. Run **"Flowbaby: Capture to Memory"**.
3. Follow the same flow as the keyboard shortcut.

### 5. Background Async Ingestion

Flowbaby processes captured memories asynchronously so your editor stays responsive.

**Timeline**:
- Capture confirmation: ~5–10 seconds.
- Background processing and graph construction: ~60–90 seconds.
- Total time from capture to searchable memory: ~1–2 minutes.

You'll receive a notification when ingestion finishes (success or failure), including a brief digest and basic metrics in the logs.

### 6. Viewing Background Operations

To inspect everything Flowbaby is doing in the background:
1. Open Command Palette.
2. Run **"Flowbaby: View Background Operations"**.
3. Review pending, running, completed, and failed ingestion tasks.

### 7. How Flowbaby Stores Summaries

In normal use, Flowbaby creates and maintains summaries for you automatically.

**Automatic summary creation**:
- When you reach an important conclusion, finish a debugging session, or make a design decision, Flowbaby may create a structured summary in the background.
- These summaries capture topic, key decisions, rationale, open questions, and next steps.
- They are stored in your workspace knowledge graph and later used by automatic search and the `@flowbaby` participant.

**Manual and agent-driven summaries (optional)**:
- You can still ask Flowbaby to create a summary explicitly if you want tighter control.
- In Copilot Chat, you can say things like:
  - `@flowbaby summarize this conversation`
  - `@flowbaby remember this session`
  - `@flowbaby create summary for this plan`
- Custom agents can also store summaries directly using Flowbaby's tools and APIs (see the Flowbaby docs at [docs.flowbaby.ai](https://docs.flowbaby.ai)).

The key idea: you don't need to manage summaries manually unless you want to - Flowbaby's automatic storage is usually enough.

### 8. How Flowbaby Retrieves Summaries

Flowbaby uses summaries as a primary source of truth when answering questions about past work.

**Automatic retrieval**:
- When context-aware search (Section 1) or the `@flowbaby` participant runs a query, Flowbaby searches both raw captured conversations and structured summaries.
- When a good match is found, you'll see a **"📚 Retrieved memories"** section with previews of the most relevant items.

**What summaries look like**:
- Organized sections: topic, key decisions, open questions, next steps, rationale.
- Rich, human-readable text designed to be useful both to you and to the LLM.

**How to prompt for past work**:
- `@flowbaby What did we decide about Plan 013?`
- `@flowbaby What questions are still open about memory transparency?`
- `@flowbaby What are the next steps for the authentication system?`
- `@flowbaby Show me the rationale for using enriched text format`

You don't need a separate "retrieve summary" command-just ask `@flowbaby` questions about previous sessions or decisions, and Flowbaby will use summaries automatically when available.

### 9. Memory Management Commands

**Toggle Memory On/Off**:
- Command Palette → "Flowbaby: Toggle Memory"
- Flips `Flowbaby.enabled` setting

**Clear Workspace Memory**:
- Command Palette → "Flowbaby: Clear Workspace Memory"
- Deletes all captured conversations for current workspace (requires confirmation)

### Automatic Detection

**`.flowbaby/venv`** This location prevents conflicts with project virtual environments (e.g., Python Jedi language server overwriting Flowbaby's dependencies).

### Unsupported Contexts

The following contexts are **not validated or supported** in this release:
- **Remote Development**: VS Code Remote-SSH, WSL, Dev Containers
- **Multi-root Workspaces**: Workspaces with multiple folder roots
- **Multi-user Workstations**: Memories are stored per workspace, not per user

Support for these contexts may be added in future releases.

### Configuration

Access settings via **File → Preferences → Settings → Extensions → Flowbaby**:

| Setting | Description | Default |
|---------|-------------|---------|
| `Flowbaby.enabled` | Enable/disable automatic memory capture and retrieval | `true` |
| `Flowbaby.maxContextResults` | Maximum number of memory results to inject into Copilot context | `3` |
| `Flowbaby.maxContextTokens` | Maximum token budget for retrieved context (higher values may increase latency and memory usage) | `32000` |
| `Flowbaby.searchTopK` | Maximum number of candidates to request from the Flowbaby search engine before ranking (normalized to be ≥ `maxContextResults`, hard-capped at 100) | `10` |
| `Flowbaby.ranking.halfLifeDays` | Recency half-life (in days) used for ranking; older memories past this window have their relevance score halved | `7` |
| `flowbaby.notifications.showIngestionSuccess` | Show toast notifications when memory ingestion completes successfully (errors are always shown) | `true` |
| `Flowbaby.pythonPath` | Python interpreter to use for the Flowbaby bridge; leave as `python3` for auto-detection of workspace `.venv`, or set an explicit path | `python3` |
| `Flowbaby.logLevel` | Logging verbosity level: `error`, `warn`, `info`, or `debug` | `info` |
| `Flowbaby.debugLogging` | Enable a dedicated debug output channel with detailed bridge and extension diagnostics | `false` |

### LLM Configuration

Configure your LLM provider via **File → Preferences → Settings → Extensions → Flowbaby**:

| Setting | Description | Default |
|---------|-------------|---------|
| `Flowbaby.llm.provider` | LLM provider (e.g., `openai`, `anthropic`, `azure`, `ollama`, `custom`) | `openai` |
| `Flowbaby.llm.model` | LLM model name (e.g., `gpt-4o-mini`, `claude-3-haiku-20240307`, `llama2`) | `gpt-4o-mini` |
| `Flowbaby.llm.endpoint` | Custom LLM endpoint URL for Azure, Ollama, or self-hosted deployments; leave empty for provider defaults | *(empty)* |

**Provider Examples**:
- **OpenAI (default)**: Leave provider as `openai`, model as `gpt-4o-mini`
- **Anthropic Claude**: Set provider to `anthropic`, model to `claude-3-opus-20240229`
- **Local Ollama**: Set provider to `ollama`, model to your model name, endpoint to `http://localhost:11434`

**Additional Examples**:
- To disable memory temporarily, set `Flowbaby.enabled` to `false` in settings

## Using Flowbaby Tools with Custom Agents

Flowbaby Chat Memory provides **Language Model Tools** that allow GitHub Copilot and custom agents to autonomously access workspace memory. These tools appear in VS Code's "Configure Tools" dialog and can be referenced in custom agent configurations.

### Quick Start

1. **Enable Tools via Configure Tools UI**:
   - Open Copilot chat → Click "Tools" icon → "Configure Tools"
   - Find the "Flowbaby" section
   - Toggle "Store Memory in Flowbaby" and "Retrieve Flowbaby Memory"
   - Toggle tools on/off individually (disabled by default for privacy)

2. **Use in Chat**:
   - Type `#flowbaby` to see autocomplete suggestions
   - Select `#flowbabyStoreSummary` or `#flowbabyRetrieveMemory`
   - Tools appear only when enabled via Configure Tools

3. **Transparency**:
   - All tool invocations logged in Output channel ("Flowbaby Agent Activity")
   - Configure Tools UI provides visual feedback for tool state

### Available Tools

#### Store Memory Tool (`#flowbabyStoreSummary`)

Stores conversation summaries in Flowbaby knowledge graph.

**Parameters**:
- `topic` (required): Summary title
- `context` (required): Summary description
- `decisions` (optional): Key decisions made
- `rationale` (optional): Reasoning behind decisions
- `metadata` (optional): Plan ID, status, etc.

#### Retrieve Memory Tool (`#flowbabyRetrieveMemory`)

Searches Flowbaby knowledge graph for relevant memories.

**Parameters**:
- `query` (required): Natural language search query
- `maxResults` (optional): Max results to return (default: 3, max: 10)

**Returns**: Both narrative markdown and structured JSON for agent parsing.

### Transparency

When agents use Flowbaby, you see:

- **Output Channel**: All tool invocations logged in "Flowbaby Agent Activity"
- **Configure Tools UI**: Visual feedback for which tools are enabled/disabled
- **Chat Autocomplete**: `#flowbaby*` commands only appear when tools are enabled
- **Workspace Logs**: Detailed bridge and ingestion logs are stored under `.flowbaby/logs` in each workspace

## Troubleshooting

### Extension Not Activating

**Check the Output Channel**:

1. Go to **View → Output**
2. Select **"Flowbaby Memory"** from the dropdown
3. Look for initialization errors

**Enable Debug Logging**: If you need more detailed information, enable `Flowbaby.debugLogging` in settings and use **"Flowbaby: Show Debug Logs"** command.

**Common Issues**:

#### 1. "LLM_API_KEY not found"

**Solution**: Use the **"Flowbaby: Set API Key"** command

Then reload VS Code: `Ctrl+Shift+P` → **"Reload Window"**

#### 2. "Python not found" or "cognee module not found"

**Solution**: 

- Run **"Flowbaby: Initialize Workspace"** to set up the environment automatically
- If using a custom Python environment, set `Flowbaby.pythonPath` to your Python path

#### 3. "No workspace folder open"

**Solution**: The extension requires a workspace (not single-file mode). Open a folder:

- **File → Open Folder**
- Or use the command: `code /path/to/your/project`

#### 4. Slow Performance

**Solutions**:

- Check that `Flowbaby.logLevel` is not set to `"debug"` (this slows down operations)
- Reduce `maxContextResults` to 1-2 for faster retrieval
- Reduce `maxContextTokens` to 1000 for lighter processing

#### 5. Capture or Retrieval Not Working

**Capture Issues**:

1. Verify keyboard shortcut (Ctrl+Alt+F / Cmd+Alt+F) is not conflicting with other extensions
2. Check Command Palette for "Flowbaby: Capture to Memory" as alternative
3. Ensure you see confirmation message after capture ("✅ Captured to memory")
4. Check Output Channel logs for ingestion errors

**Retrieval Issues**:

1. Verify `Flowbaby.enabled` is `true` in settings
2. Type `@flowbaby` in chat to invoke the participant explicitly
3. Check Output Channel logs for retrieval attempts and timing
4. Remember: The first conversation in a new workspace has no context (memory starts empty)
5. Each workspace has separate memory-switching workspaces means different context
6. If retrieval fails, you'll see "⚠️ Memory retrieval unavailable" but participant continues without context

**Note**: Auto-detection works for standard `.venv` setups on Linux, macOS, and Windows. For remote contexts (Remote-SSH, WSL, Dev Containers), conda, or pyenv, use explicit `Flowbaby.pythonPath` configuration.

### Clearing Memory

To reset your workspace memory (e.g., to start fresh or clear sensitive data):

Use `Flowbaby: Clear Workspace Memory` command from the command pallette. This moves your current data to .flowbaby/.trash until you decide to delete it permanently. 

The extension will reinitialize on next activation, creating a fresh knowledge graph.

## Privacy and Data Storage

- **Local-Only Operation** - All data stays on your local machine; no cloud services involved
- **No Telemetry** - The extension does not collect analytics or usage data
- **Workspace Isolation** - Each workspace has isolated memory in its own directories; no cross-project data leakage
- **API Key Security** - Your API key is never logged or transmitted except to your configured LLM provider
- **Data Location** - Memory is stored in workspace-local directories: `.flowbaby/system/` (system data) and `.flowbaby/data/` (knowledge graph data)

To completely remove all extension data from a workspace:

```bash
rm -rf .flowbaby/ .flowbaby/system/ .flowbaby/data/  # In workspace root
```

## Known Limitations

- **Workspace Required** - Extension doesn't work in single-file mode
- **Python Dependency** - Requires Python 3.8+ on your system; Cognee and dependencies are installed automatically in an isolated environment
- **Platform Support** - Primarily tested on macOS and Linux; Windows support may require additional configuration

## Screenshots

### Automatic Context Search

![Automatic Search](https://raw.githubusercontent.com/groupzer0/flowbaby-issues/main/media/auto-search.png)

*Flowbaby automatically searches memory based on chat context*

### Automatic Memory Storage

![Automatic Store](https://raw.githubusercontent.com/groupzer0/flowbaby-issues/main/media/auto-store.png)

*Flowbaby knows when to store memories based on conversation cues*

### Interactive Chat Participant

![Interactive Chat](https://raw.githubusercontent.com/groupzer0/flowbaby-issues/main/media/interactive-chat-participant.png)

*Use @flowbaby to query your workspace memory*

### Manual Memory Storage

![Manual Store](https://raw.githubusercontent.com/groupzer0/flowbaby-issues/main/media/manual-store-summary.png)

*Capture important context with keyboard shortcuts*

## Contributing

We welcome contributions! See [discussions](https://github.com/groupzer0/flowbaby-issues/discussions)

## License

This extension is licensed under the PolyForm Strict License 1.0.0. See [LICENSE](https://github.com/lsalsich/flowbaby/blob/main/LICENSE) for details.

This extension uses [Cognee](https://github.com/topoteretes/cognee) under the Apache 2.0 license. See [THIRD_PARTY_NOTICES.md](./third-party-notices) for details.

## Support

- **Report Bugs**: [GitHub Issues](https://github.com/groupzer0/flowbaby-issues/issues)
- **Feature Requests**: [GitHub Discussions](https://github.com/groupzer0/flowbaby-issues/discussions)

## Changelog

See [CHANGELOG.md](./changelog) for version history and release notes. 

---
