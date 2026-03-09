# Persistent Engineer Agent

> **January 2026** -- The first version of the always-on AI coding agent daemon.
>
> **This project is archived.** It evolved through v2 and v3 into [AgentCore](https://github.com/NodeNestor), the production agent infrastructure now on the NodeNestor organization.

---

## What is this?

Persistent Engineer was an experiment in building a continuously running AI employee -- a daemon process that stays alive, receives tasks over Redis, and operates with full OS-level access inside a Docker container. Unlike ephemeral "run once and exit" agent patterns, this agent persists across tasks: it keeps its workspaces, credentials, memory, and desktop session alive indefinitely.

The core idea: give Claude Code a full Linux desktop (Xvfb + VNC), multiple managed workspaces, an encrypted credential vault, and a set of MCP tools -- then let it run autonomously for hours using structured development loops.

## Architecture

```
+---------------------+     WebSocket/Redis     +----------------------+
|   Web Platform      |<----------------------->|  Persistent Agent    |
|   (Blazor Server)   |                         |     Container        |
|                     |                         |                      |
|  - Chat UI          |                         |  - Daemon Process    |
|  - Workspace Mgmt   |                         |  - Claude Code       |
|  - Credential Mgmt  |                         |  - Desktop Control   |
|  - VNC Viewer       |                         |  - MCP Tools         |
+---------------------+                         +----------------------+
         |                                               |
         v                                               v
+---------------------+                         +----------------------+
|  Message Queue      |                         |  Persistent Storage  |
|  (Redis)            |                         |  - /workspace/       |
+---------------------+                         +----------------------+
```

## Key Features

- **Persistent operation** -- runs continuously, receives tasks via Redis pub/sub
- **Full desktop control** -- Xvfb + X11VNC + noVNC for screenshots, mouse, keyboard, window management
- **Multiple workspaces** -- manage independent Git repositories with per-workspace credentials
- **Encrypted credential vault** -- Fernet (AES-128-CBC) encrypted secret storage
- **Autonomous development loops** -- `/build-test-loop` and `/project-loop` plugins for multi-hour unattended coding
- **Cross-platform** -- Linux (Ubuntu 24.04) and Windows Server containers
- **MCP tool suite** -- desktop-control, filesystem, code-structure-analyzer, agent-memory, git-publisher, context7, playwright

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Daemon | Python 3 (asyncio, Redis pub/sub) |
| Container | Docker, Ubuntu 24.04 / Windows Server |
| Desktop | Xvfb, X11VNC, noVNC, Openbox |
| Message Queue | Redis 7 |
| Database | SQL Server 2022 (for testing) |
| MCP Tools | Python, .NET 8, Node.js 22 |
| Web UI | Blazor Server (separate repo) |
| AI | Claude Code with custom plugins |
| Runtimes | Node.js 22, .NET 8/9/10, Rust, Python 3, Go, Java |

## Evolution Chain

```
PersistentEngineer v1 (this repo, Jan 2026)
    |
    v
PersistentEngineer v2 (multi-agent coordination)
    |
    v
PersistentEngineer v3 (refined loop system)
    |
    v
AgentCore (production infrastructure, NodeNestor org)
```

## Project Structure

```
PersistentEngineer/
  Dockerfile.linux           # Ubuntu 24.04 container
  Dockerfile.windows         # Windows Server container
  daemon-entrypoint.sh       # Linux startup (Xvfb, VNC, daemon)
  daemon-entrypoint.ps1      # Windows startup
  docker-compose.yml         # Linux compose stack
  docker-compose.windows.yml # Windows compose stack
  supervisor.conf            # Process management
  CLAUDE.md                  # Agent instructions (injected into container)
  daemon/
    daemon.py                # Main event loop (Redis sub, Claude Code subprocess)
    workspace_manager.py     # Multi-workspace support
    loop_handler.py          # Autonomous loop orchestration
    refresh_credentials.py   # OAuth token refresh
  credentials/
    vault.py                 # Fernet-encrypted credential vault
  mcp-tools/
    mcp.json                 # MCP server configuration
    desktop-control/         # Full OS control MCP server
    agent-network/           # Agent-to-agent communication
    AgentMemory/             # Persistent memory (.NET)
    CodeStructureAnalyzer/   # Code parsing (.NET)
    GitPublisher/            # Git operations (.NET)
  plugins/
    build-test-loop/         # Autonomous dev loop plugin
```

## License

MIT

---

*Built January 2026. Archived as a historical snapshot of the agent infrastructure evolution.*
