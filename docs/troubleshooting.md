---
sidebar_position: 6
---

# Troubleshooting

Common issues and solutions for Flowbaby.


## Extension Not Activating

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
- If the Python bridge daemon is unstable in your environment, switch `Flowbaby.bridgeMode` to `"spawn"` to fall back to the legacy per-request process model.

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

**Note**: Auto-detection prefers a workspace-managed `.flowbaby/venv` when present. For remote contexts (Remote-SSH, WSL, Dev Containers), conda, pyenv, or other non-standard setups, use an explicit `Flowbaby.pythonPath`.

## Clearing Memory

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
- **Python Dependency** - Requires Python 3.10-3.12 on your system; Cognee and dependencies are installed automatically in an isolated environment
- **Platform Support** - Primarily tested on macOS and Linux; Windows support may require additional configuration
