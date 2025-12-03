---
sidebar_position: 3
---

# Configuration

Customize Flowbaby's behavior through VS Code settings.


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
