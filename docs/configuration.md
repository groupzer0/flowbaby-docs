---
sidebar_position: 3
---

# Configuration

Customize Flowbaby's behavior through VS Code settings.

## Automatic Detection

**`.flowbaby/venv`** This location prevents conflicts with project virtual environments (e.g., Python Jedi language server overwriting Flowbaby's dependencies).

## Unsupported Contexts

The following contexts are **not validated or supported** in this release:
- **Remote Development**: VS Code Remote-SSH, WSL, Dev Containers
- **Multi-root Workspaces**: Workspaces with multiple folder roots
- **Multi-user Workstations**: Memories are stored per workspace, not per user

Support for these contexts may be added in future releases.

## Configuration

Access settings via **File → Preferences → Settings → Extensions → Flowbaby**:

| Setting | Description | Default |
|---------|-------------|---------|
| `Flowbaby.enabled` | Enable/disable automatic memory capture and retrieval | `true` |
| `Flowbaby.maxContextResults` | Maximum number of memory results to inject into Copilot context | `3` |
| `Flowbaby.maxContextTokens` | Maximum token budget for retrieved context (higher values may increase latency and memory usage) | `32000` |
| `Flowbaby.searchTopK` | Maximum number of candidates to request from the Flowbaby search engine before ranking (normalized to be ≥ `maxContextResults`, hard-capped at 100) | `10` |
| `Flowbaby.ranking.halfLifeDays` | Recency half-life (in days) used for ranking; older memories past this window have their relevance score halved | `7` |
| `Flowbaby.bridgeMode` | Bridge execution mode: `daemon` (default, long-lived Python process for faster requests) or `spawn` (legacy per-request process; useful for troubleshooting daemon issues) | `daemon` |
| `Flowbaby.daemonIdleTimeoutMinutes` | Minutes of inactivity before the bridge daemon exits; lower values save memory, higher values keep the daemon warm at the cost of a long-lived process | `30` |
| `flowbaby.notifications.showIngestionSuccess` | Show toast notifications when memory ingestion completes successfully (errors are always shown) | `true` |
| `flowbaby.showRetrievalNotifications` | Show a notification when Flowbaby retrieves relevant memories | `true` |
| `Flowbaby.pythonPath` | Path to Python interpreter. Leave empty for auto-detection (uses `.flowbaby/venv` if present, otherwise system Python). Set an explicit path to override auto-detection. | *(empty)* |
| `Flowbaby.logLevel` | Logging verbosity level: `error`, `warn`, `info`, or `debug` | `info` |
| `Flowbaby.debugLogging` | Enable a dedicated debug output channel with detailed bridge and extension diagnostics | `false` |
| `Flowbaby.synthesis.modelId` | **Copilot synthesis model** used to synthesize answers from retrieved memory context (does not affect the Python bridge LLM) | `gpt-5-mini` |

## LLM Configuration

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
