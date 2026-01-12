---
sidebar_position: 6
---

# Troubleshooting

Common issues and solutions for Flowbaby.


**Status bar shows warning**: Click to see current state. Common states:
- "Cloud Login Required" → Run `Flowbaby Cloud: Login`
- "Setup Required" → Run `Flowbaby: Initialize Workspace`

**Logs**: View → Output → select "Flowbaby" or "Flowbaby Cloud" channel.

**Debug mode**: Enable `Flowbaby.debugLogging` for verbose diagnostics.

**Clear context**: `Flowbaby: Clear Workspace Memory` moves data to `.flowbaby/.trash/` (recoverable).

---

## Privacy

- All context storage is local to the workspace
- Only chat content already sent to GitHub Copilot is summarized
- No source code is analyzed or transmitted
- Cloud authentication uses GitHub OAuth; credentials are short-lived
- Audit log written to `.flowbaby/logs/audit.jsonl`

---

## License

MIT License + Commons Clause. See [LICENSE](https://github.com/groupzer0/flowbaby/blob/main/LICENSE) for details.

Uses [Cognee](https://github.com/topoteretes/cognee) (Apache 2.0) for knowledge graph operations.

---

## Reference Implementation

- **Flowbaby Agent Team**  
  A set of Copilot agents and skills designed to fully leverage Flowbaby’s persistent memory model.  
  This is the recommended reference implementation for evaluating Flowbaby’s impact. [Flowbaby Agent Team](https://github.com/groupzer0/vs-code-agents)

---

## Links

- [GitHub Repository](https://github.com/groupzer0/flowbaby)
- [Issues & Bug Reports](https://github.com/groupzer0/flowbaby/issues)
