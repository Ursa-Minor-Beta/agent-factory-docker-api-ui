# Agent Factory Deploy

Docker deployment for Agent Factory ([agent-factory](https://github.com/Ursa-Minor-Beta/agent-factory) + [agent-factory-ui](https://github.com/Ursa-Minor-Beta/agent-factory-ui)).

> **⚠️ Note:** Agent Factory is actively being built. There could be breaking changes.

## Features

**Building agents**
- **Node Graph Workflows** - Visual drag-and-drop editor with 11 node types (LLM, HTTP, JS, branch, sub-agent, memory store/search/update/delete), undo/redo, disabling nodes, templates, and `{{node:/secret:/var:}}` reference suggestions
- **Agent Builder** - Chat with a builder agent that designs multi-agent systems: it edits nodes on the canvas, creates sub-agents, names the agent, assigns a workspace, saves and test-runs it, and sets up schedules and webhooks
- **Composable Agents** - Agents can call other agents as sub-workflows or LLM tools
- **Agent Relations Graph** - See which agents call which, across all agents or one workspace
- **Run in Editor** - Run with saved inputs, live node status and an animated step-by-step replay on the canvas

**LLMs and tools**
- **Multi-Provider LLM** - Anthropic, OpenAI, Ollama, DeepSeek, Groq, Mistral, OpenRouter, Together AI, xAI, plus any OpenAI-compatible endpoint (vLLM, LM Studio, LiteLLM, ...)
- **Remote MCP Servers** - Anthropic and OpenAI LLM nodes can use tools from remote MCP servers
- **Builtin Tools** - LLM function calling for agents, memory, collections, session notes, schedules and webhooks
- **Memory & Collections** - Persistent structured storage with schemas, semantic search and JSON export/import

**Triggers and runs**
- **Streaming Chat** - Real-time SSE streaming with sessions, tool call display and message queueing
- **Schedules** - Cron (down to every 10 seconds, timezone-aware), one-time or fixed-interval runs, with next-run preview, run now, pause, failure auto-stop, and incognito or chat-session runs
- **Webhooks** - Public URLs that start agent runs, with optional sandboxed JS handlers (Slack, GitHub, Telegram templates) and a test panel
- **Execution Monitoring** - Node-level timing, logs, tool calls, token usage, child run tree, and what triggered each run

**Management**
- **Workspaces** - Isolate agents, secrets, variables, collections and providers per workspace
- **Encrypted Secrets** (AES-256-GCM, `{{secret:KEY}}`) and plain-text **Variables** (`{{var:KEY}}`)
- **File Storage** - Built-in file management for agent artifacts
- **Export/Import** - Agents with their dependencies, or whole workspaces, as JSON packages
- **GitHub Sync** - Link agents to repos with push/pull
- **Agent Snapshots** - Version history with restore
- **Multi-User** - JWT auth, API keys, and Admin/User roles
- **Dark/Light Theme** - Responsive UI for desktop, tablet and mobile
- **Swagger Docs** - Full API documentation at `/docs` (e.g. http://localhost:3000/docs)

## Quick Start

1. Copy environment file:
```bash
cp .env.example .env
```

2. Edit `.env` with your configuration (at minimum set `ADMIN_EMAIL` and `ADMIN_PASSWORD`)

3. Run with local MongoDB:
```bash
docker-compose --profile with-db up --build
```

Or with external MongoDB (set `MONGODB_URI` in `.env`):
```bash
docker-compose up --build
```

4. Access:
   - UI: http://localhost:8080
   - API: http://localhost:3000
## Deploy Specific Version

```bash
API_VERSION=<branch/tag> UI_VERSION=<branch/tag> docker-compose up --build
```

## Update After Branch Changes

A regular build picks up new commits automatically — no `--no-cache` needed:
```bash
docker-compose --profile with-db up --build
```

How caching works (requires BuildKit, the default in current Docker):
- The repos are fetched via `ADD <git url>#<ref>`, which checks the remote commit on every build and re-fetches only when it changed.
- `npm ci` is re-run only when `package.json` / `package-lock.json` change; code-only changes just re-run `npm run build`.
- The npm download cache is kept between builds, so dependency changes install faster.

To also pull a newer Node base image (`node:26-alpine`), add `--pull` occasionally:
```bash
docker-compose --profile with-db build --pull && docker-compose --profile with-db up
```

Use `--no-cache` only if the build cache itself seems broken.

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `API_VERSION` | API branch/tag to deploy | `master` |
| `UI_VERSION` | UI branch/tag to deploy | `master` |
| `API_PORT` | API exposed port | `3000` |
| `UI_PORT` | UI exposed port | `8080` |
| `MONGO_PORT` | MongoDB exposed port | `27017` |
| `TRUST_PROXY` | Reverse proxy hops in front of the API (set `1` behind Caddy/Nginx to get real client IPs) | `0` |
| `MONGO_USER` | MongoDB root username | - |
| `MONGO_PASSWORD` | MongoDB root password | - |
| `JWT_SECRET` | JWT signing secret | - |
| `ENCRYPTION_KEY` | Encryption key (32 bytes) | - |
| `MONGODB_URI` | MongoDB connection string | `mongodb://mongo:27017/agent-factory` |
| `VITE_API_URL` | API URL for UI | `http://localhost:3000` |

## Production Deployment

For production, ensure you:
1. Set strong `JWT_SECRET`, `ENCRYPTION_KEY`, `ADMIN_PASSWORD`
2. Configure MongoDB with authentication (e.g., `mongodb://user:password@host:27017/agent-factory`)
3. Set `VITE_API_URL` to your production API URL
4. Consider using a reverse proxy (nginx/traefik) for SSL
