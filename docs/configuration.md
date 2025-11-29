---
sidebar_position: 3
---

# Configuration

Customize Flowbaby's behavior through VS Code settings.


Flowbaby tools are controlled exclusively through VS Code's **Configure Tools** UI (see Quick Start below). No additional workspace settings are required for authorization.

### Documentation

- **Complete API Guide**: [AGENT_INTEGRATION.md](./agent-integration.md)
  - TypeScript examples and error handling
  - Security model and best practices
  - Troubleshooting guide
- **Bridge Contract**: [bridge/INGEST_CONTRACT.md](https://github.com/lsalsich/flowbaby/blob/main/extension/bridge/INGEST_CONTRACT.md)
  - JSON schema specification
  - Error codes reference
  - Performance characteristics
- **Test Agent**: [test-agent/](https://github.com/lsalsich/flowbaby/blob/main/test-agent/)
  - Reference implementation validating all scenarios
  - Can be used as template for custom agents

### Automatic Detection

**`.flowbaby/venv`?** This location prevents conflicts with project virtual environments (e.g., Python Jedi language server overwriting Flowbaby's dependencies). The `.flowbaby/` directory is also automatically added to `.gitignore`.

### When to Configure Manually

Set `Flowbaby.pythonPath` explicitly if:
- Virtual environment is outside workspace directory
- Using conda or pyenv environments (not auto-detected)
- Want to share a Python environment across multiple workspaces

Example configuration in VS Code settings:

```json
{
  "Flowbaby.pythonPath": "/path/to/your/.flowbaby/venv/bin/python"
}
```

**Platform-specific examples**:
- Linux/macOS: `/home/user/project/.flowbaby/venv/bin/python`
- Windows: `C:\Users\user\project\.flowbaby\venv\Scripts\python.exe`

### Unsupported Contexts (v0.2.0)

The following contexts are **not validated or supported** in this release:
- **Remote Development**: VS Code Remote-SSH, WSL, Dev Containers
- **Multi-root Workspaces**: Workspaces with multiple folder roots
- **Conda Environments**: Automatic detection not implemented (use explicit config)
- **Pyenv Environments**: Automatic detection not implemented (use explicit config)

Support for these contexts may be added in future releases.

### Configuration

Access settings via **File → Preferences → Settings → Extensions → Flowbaby Memory**:

| Setting | Description | Default |
|---------|-------------|---------|
| `Flowbaby.enabled` | Toggle memory capture and retrieval on/off | `true` |
| `Flowbaby.maxContextResults` | Maximum number of memory results to retrieve | `3` |
| `Flowbaby.maxContextTokens` | Token budget for retrieved context | `32000` |
| `Flowbaby.searchTopK` | Maximum candidates to request from search engine before ranking | `10` |
| `Flowbaby.ranking.halfLifeDays` | Half-life (in days) for recency-aware ranking | `7` |
| `Flowbaby.autoIngestConversations` | **Experimental**: Auto-capture @flowbaby conversations | `false` |
| `flowbaby.notifications.showIngestionSuccess` | Show success notifications for memory ingestion | `true` |
| `Flowbaby.pythonPath` | Path to Python interpreter | `python3` |
| `Flowbaby.logLevel` | Logging verbosity: error, warn, info, debug | `info` |
| `Flowbaby.debugLogging` | Show debug output channel (for troubleshooting) | `false` |
### LLM Configuration

Configure your LLM provider via **File → Preferences → Settings → Extensions → Flowbaby Memory**:

| Setting | Description | Default |
|---------|-------------|---------|
| `Flowbaby.llm.provider` | LLM provider (openai, anthropic, ollama, etc.) | `openai` |
| `Flowbaby.llm.model` | Model name (e.g., gpt-4o-mini, claude-3-opus) | `gpt-4o-mini` |
| `Flowbaby.llm.endpoint` | Custom API endpoint (for self-hosted models) | *(empty)* |

**Provider Examples**:
- **OpenAI (default)**: Leave provider as `openai`, model as `gpt-4o-mini`
- **Anthropic Claude**: Set provider to `anthropic`, model to `claude-3-opus-20240229`
- **Local Ollama**: Set provider to `ollama`, model to your model name, endpoint to `http://localhost:11434`

**Additional Examples**:
- To disable memory temporarily, set `Flowbaby.enabled` to `false` in settings
- To enable experimental auto-capture of @flowbaby conversations (feedback loop), set `Flowbaby.autoIngestConversations` to `true`

## Using Flowbaby Tools with Custom Agents

Flowbaby Chat Memory provides **Language Model Tools** that allow GitHub Copilot and custom agents to autonomously access workspace memory. These tools appear in VS Code's "Configure Tools" dialog and can be referenced in custom agent configurations.

### Quick Start

1. **Enable Tools via Configure Tools UI**:
   - Open Copilot chat → Click "Tools" (⚙️ icon) → "Configure Tools"
   - Find "Store Memory in Flowbaby" and "Retrieve Flowbaby Memory"
   - Toggle tools on/off individually (disabled by default for privacy)

2. **Use in Chat**:
   - Type `#flowbaby` to see autocomplete suggestions
   - Select `#flowbabyStoreSummary` or `#flowbabyRetrieveMemory`
   - Tools appear only when enabled via Configure Tools

3. **Transparency**:
   - All tool invocations logged in Output channel ("Flowbaby Agent Activity")
   - Configure Tools UI provides visual feedback for tool state

### Custom Agent Example

Create a `.agent.md` file in your workspace to define a memory-aware agent:

```markdown
---
name: Memory-Aware Code Assistant
description: Copilot assistant with access to workspace memory
tools: ['search', 'flowbabyStoreSummary', 'flowbabyRetrieveMemory']
---

You are a code assistant with access to workspace-specific memory.

When the user asks about past decisions or implementations:
1. Use #flowbabyRetrieveMemory to search for relevant context
2. Ground your answer in the retrieved memories
3. If no memories exist, use your training data but clarify it's not workspace-specific

When the user completes an important implementation or makes a decision:
1. Offer to store a summary using #flowbabyStoreSummary
2. Include topic, context, and key decisions in the summary
```

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

### Agent Integration Settings

### Transparency

When agents use Flowbaby, you see:

- **Output Channel**: All tool invocations logged in "Flowbaby Agent Activity"
- **Configure Tools UI**: Visual feedback for which tools are enabled/disabled
- **Chat Autocomplete**: `#flowbaby*` commands only appear when tools are enabled

### For Extension Developers

See [AGENT_INTEGRATION.md](./agent-integration.md) for:
- Complete API documentation with TypeScript examples
- Command signatures (`Flowbaby.ingestForAgent`, `Flowbaby.retrieveForAgent`)
- Error codes and handling strategies
- Request/response schemas
- Testing and troubleshooting guides

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
5. Each workspace has separate memory—switching workspaces means different context
6. If retrieval fails, you'll see "⚠️ Memory retrieval unavailable" but participant continues without context

### Common Error Patterns

| Symptom | Likely Cause | Recommended Action |
|---------|--------------|-------------------|
| "Python script exited with code 1" (empty stderr) | Interpreter mismatch: `cognee` or `python-dotenv` not installed in detected Python environment | Set `Flowbaby.pythonPath` in VS Code settings to correct interpreter (Linux/macOS: `.venv/bin/python`, Windows: `.venv\Scripts\python.exe`) |
| "No module named 'cognee'" | Missing `cognee` package in Python environment | Run **"Flowbaby: Initialize Workspace"** to set up environment automatically |
| "LLM_API_KEY not found" | Missing API key in `.env` file or environment | Create `.env` file in workspace root with valid `LLM_API_KEY`, then reload window |
| Script timeout (retrieval: 15s, ingestion: 120s) | Network issues, slow LLM provider, or filesystem delay | Ingestion timeout is generous (120s); if timeout occurs but data appears via @flowbaby, ingestion succeeded in background. Check Output Channel for timing metrics. |
| JSON parse error in logs | Script produced non-JSON output | Report as bug. Check for conflicting print statements in bridge scripts. |

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

![Automatic Search](/img/flowbaby/auto-search.png)

*Flowbaby automatically searches memory based on chat context*

### Automatic Memory Storage

![Automatic Store](/img/flowbaby/auto-store.png)

*Flowbaby knows when to store memories based on conversation cues*

### Interactive Chat Participant

![Interactive Chat](/img/flowbaby/interactive-chat-participant.png)

*Use @flowbaby to query your workspace memory*

### Manual Memory Storage

![Manual Store](/img/flowbaby/manual-store-summary.png)

*Capture important context with keyboard shortcuts*

## Contributing

We welcome contributions! See [CONTRIBUTING.md](https://github.com/lsalsich/flowbaby/blob/main/CONTRIBUTING.md) for:

- Development setup instructions
- How to run tests
- Code style guidelines
- Pull request process

### Debugging

For extension developers:

#### Launch Extension Development Host

1. Open the `extension/` folder in VS Code (not the repository root)
2. Press **F5** in VS Code
3. New window opens with extension loaded
4. Check Debug Console for activation logs (View → Debug Console)

#### Set Breakpoints

- Click left margin in TypeScript files to set breakpoints
- Breakpoints pause execution in Extension Host
- Source maps enable debugging original TypeScript code

#### View Logs

The extension outputs logs to different locations:

- **Debug Console**: Extension activation and runtime logs (View → Debug Console)
- **Output Channel**: FlowbabyClient bridge operations—select "Flowbaby Memory" from dropdown (View → Output)
- **Developer Tools**: Extension Host errors—open with Help → Toggle Developer Tools

#### Test Changes

After modifying code:

- **Rebuild**: Run `npm run compile` in the `extension/` directory
- **Reload**: Press **Ctrl+R** (or **Cmd+R** on Mac) in the Extension Development Host window
- **Auto-compile**: The `preLaunchTask` in `.vscode/launch.json` auto-compiles when pressing F5

## License

This extension is licensed under the PolyForm Strict License 1.0.0. See [LICENSE](https://github.com/lsalsich/flowbaby/blob/main/LICENSE) for details.

This extension uses [Cognee](https://github.com/topoteretes/cognee) under the Apache 2.0 license. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for details.

## Support

- **Report Bugs**: [GitHub Issues](https://github.com/lsalsich/flowbaby-issues/issues)
- **Feature Requests**: [GitHub Discussions](https://github.com/lsalsich/flowbaby-issues/discussions)

## Changelog

See [CHANGELOG.md](./changelog) for version history and release notes.

---
