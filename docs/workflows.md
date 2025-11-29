---
sidebar_position: 4
---

# Workflows

Learn effective patterns for using Flowbaby in your daily development.

## Usage

### Environment Management (v0.3.14+)

Flowbaby includes tools to keep your environment healthy:

- **Status Bar**: Shows real-time health (Ready, Setup Required, Refreshing, Error)
- **Refresh Dependencies**: If you encounter issues, run **"Flowbaby: Refresh Bridge Dependencies"** to safely reinstall the environment without losing data.
- **Background Safety**: Refresh operations automatically pause background tasks to prevent conflicts.

### Async Ingestion Behavior (v0.3.3+)

Starting in v0.3.3, memory ingestion operates asynchronously to prevent blocking your workflow. Here's what to expect:

**Timing Expectations**:

- **Capture Response**: 5–10 seconds—you can continue working immediately after capture
- **Background Processing**: 60–90 seconds—knowledge graph construction happens in the background
- **Total Time**: ~1-2 minutes from capture to searchable memory

**Staged Messaging**:

When you capture a memory (via keyboard shortcut, command palette, or agent tools), you'll see:

> **"Memory staged – processing will finish in ~1–2 minutes. You'll get a notification when it's done."**

This means:
- ✅ Your content has been safely staged for processing
- ✅ You can continue working without waiting
- ⏳ Knowledge graph construction is running in the background
- 🔔 You'll receive a notification when processing completes

**Completion Notifications**:

After background processing finishes, you'll receive one of two notifications:

- **Success** (ℹ️ Info): "✅ Flowbaby finished" with workspace name, summary digest, elapsed time, and entity count. Click "View Status" to see all background operations.
- **Failure** (⚠️ Warning): "⚠️ Flowbaby failed" with workspace name, summary digest, and remediation guidance. Click "Retry" to re-process or "View Logs" for error details.

**Why Async?**

Previously, memory capture blocked for 60-90 seconds while the knowledge graph was being built. This made agents unresponsive and disrupted your workflow. With async ingestion:

- Agents return responses in &lt;10 seconds
- You can store multiple memories without waiting
- Background processing doesn't interrupt your work
- Background processing doesn't interrupt your work

**Background Status**:

To check all in-flight operations:
1. Press `Cmd+Shift+P` (or `Ctrl+Shift+P` on Linux/Windows)
2. Type "Flowbaby: View Background Operations"
3. View pending, running, completed, and failed operations

### Capturing Conversations

**Keyboard Shortcut (Primary Method)**:
1. View a valuable chat message (from any participant: @workspace, @terminal, GitHub Copilot, etc.)
2. Press **Ctrl+Alt+F** (or **Cmd+Alt+F** on macOS)
3. Paste the message content in the input box (or leave empty to use clipboard)
4. Press Enter to capture
5. See "Memory staged – processing will finish in ~1–2 minutes. You'll get a notification when it's done." confirmation
6. Continue working—you'll receive a completion notification when ready

**Command Palette (Alternative)**:
1. Open Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Type "Flowbaby: Capture to Memory"
3. Follow the same workflow as keyboard shortcut

**What Gets Captured**:
- Chat conversations from ANY participant (@workspace, @terminal, GitHub Copilot, etc.)
- Manual notes and observations you type
- Code explanations and discussions
- Only content YOU choose to capture (selective, user-controlled)

### Retrieving Context with @flowbaby

**How to Use**:
1. Open GitHub Copilot Chat (`Ctrl+Alt+I` or click chat icon)
2. Type: `@flowbaby How did I implement caching?`
3. The participant:
   - Retrieves relevant memories from your workspace knowledge graph
   - Shows previews: "📚 Retrieved memories"
   - Augments your question with retrieved context
   - Generates a contextually-aware response
   - Optionally captures the conversation for future retrieval (if enabled via config)

**Example Queries**:
- `@flowbaby What problems did we discuss about the authentication system?`
- `@flowbaby What solutions did we consider for rate limiting?`
- `@flowbaby Summarize our decisions about database architecture`

### Creating Conversation Summaries (Plan 014)

**What are Conversation Summaries?**

Conversation summaries are structured records that capture the essence of a chat session, including:
- Main topic and context
- Key decisions made
- Rationale behind decisions
- Open questions still unresolved
- Next steps to take
- References to files, plans, or other resources

Summaries are more valuable than raw chat logs because they're organized, searchable, and focus on what matters most.

**When to Create Summaries**:

Create a summary when you've:
- Completed a design discussion with important decisions
- Resolved a complex debugging issue with lessons learned
- Made architectural choices that should be remembered
- Discussed tradeoffs between different approaches
- Reached conclusions about implementation direction

**How to Create a Summary**:

1. **Open GitHub Copilot Chat** (`Ctrl+Alt+I` / `Cmd+Alt+I`)
2. **Start a conversation with @flowbaby** and type:
   - `@flowbaby summarize this conversation`
   - Or: `@flowbaby remember this session`
   - Or: `@flowbaby create summary`

3. **Review the scope preview**:
   - Extension shows: "I'll summarize the last 15 turns (from 5 mins ago)."
   - Default is last 15 conversation turns

4. **Adjust turn count (optional)**:
   - Type a number to change scope: `30` → "I'll summarize the last 30 turns..."
   - Repeat to iteratively adjust: `20` → "I'll summarize the last 20 turns..."
   - Say `confirm` when ready to proceed

5. **Review the generated summary**:
   - Extension uses LLM to analyze conversation history
   - Displays structured summary with all sections (Topic, Context, Decisions, etc.)
   - Shows metadata: Status, timestamps, plan IDs if detected

6. **Confirm storage**:
   - Extension asks: "Should I store this summary in Flowbaby memory?"
   - Reply `yes`, `store it`, or `save` to confirm
   - Reply `no` or `cancel` to discard
   - Summary is only stored after explicit confirmation

### Retrieving Summaries

When you query `@flowbaby`, the extension searches both raw captured conversations and structured summaries. Summaries display with rich metadata when available.

**What You'll See in Retrieval Results**:

Enriched summaries:

- **Structured sections**: Topic, Key Decisions, Open Questions, Next Steps
- **Full transparency**: Up to 32000 characters shown with explicit truncation indicator if longer

**Example Retrieval Queries**:

- `@flowbaby What did we decide about Plan 013?` → Retrieves relevant summaries with decisions highlighted
- `@flowbaby What questions are still open about memory transparency?` → Finds Open Questions sections from summaries
- `@flowbaby What are the next steps for the authentication system?` → Retrieves Next Steps from related summaries
- `@flowbaby Show me the rationale for using enriched text format` → Finds Rationale sections explaining design choices

**Retrieval Benefits**:

- **Faster answers**: Structured summaries surface key points without reading full conversations
- **Better context**: LLM sees organized decisions/rationale instead of scattered chat logs
- **Temporal awareness**: Timestamps help distinguish recent vs historical decisions
- **Status tracking**: Know if decisions are still Active or have been Superseded

### Memory Management Commands

**Toggle Memory On/Off**:
- Command Palette → "Flowbaby: Toggle Memory"
- Flips `Flowbaby.enabled` setting

**Clear Workspace Memory**:
- Command Palette → "Flowbaby: Clear Workspace Memory"
- Deletes all captured conversations for current workspace (requires confirmation)
