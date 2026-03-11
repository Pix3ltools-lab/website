---
title: "Pix3lMCP: Managing Pix3lBoard from Claude Code"
description: "How to install Pix3lMCP and use it from Claude Code to manage boards, lists, and cards directly from your AI assistant."
category: "Pix3lMCP"
---

# Pix3lMCP

Pix3lMCP is an MCP (Model Context Protocol) server that exposes Pix3lBoard as tools for Claude Code and Claude.ai. It allows your AI assistant to create, read, update, and delete boards, lists, and cards in Pix3lBoard — without leaving your workflow.

**Repository:** [github.com/Pix3ltools-lab/pix3lmcp](https://github.com/Pix3ltools-lab/pix3lmcp)

## Tech Stack

- **Language:** TypeScript
- **Protocol:** Model Context Protocol (MCP)
- **Transport:** Streamable HTTP (stateless)
- **Vercel variant:** Next.js 15 API route
- **Docker variant:** Express server
- **Validation:** Zod
- **Build:** esbuild (Docker), Next.js build (Vercel)

## Available Tools

Pix3lMCP exposes 17 tools:

| Tool | Description |
|------|-------------|
| `list_workspaces` | List accessible workspaces |
| `list_boards` | List boards in a workspace |
| `get_board` | Get details of a specific board |
| `create_board` | Create a new board |
| `update_board` | Update board name or description |
| `delete_board` | Delete a board |
| `list_lists` | List all lists in a board |
| `create_list` | Create a new list |
| `update_list` | Update list name or position |
| `delete_list` | Delete a list |
| `list_cards` | List cards in a board or list |
| `get_card` | Get full details of a card |
| `create_card` | Create a new card |
| `update_card` | Update card fields |
| `move_card` | Move a card to a different list |
| `archive_card` | Archive a card |
| `delete_card` | Permanently delete a card |

### Card Fields

When creating or updating a card, the following fields are available:

- `title`, `description`
- `type` — Task, Bug, Feature, Meeting, Music, Video, Image, Audio, Text
- `priority`, `severity`, `effort`
- `tags`, `due_date`, `responsible`, `job_number`
- `rating` (1–5)
- `links`, `checklist`
- `prompt`, `ai_tool` — AI-specific fields

## Environment Variables

```env
# Required: URL of your Pix3lBoard instance
PIX3LBOARD_URL=http://localhost:3000

# Authentication — choose ONE option:

# Option 1 (preferred): API Key
PIX3LBOARD_API_KEY=pk_live_xxxxxxxxxxxx

# Option 2 (fallback): Email + Password
PIX3LBOARD_EMAIL=admin@example.com
PIX3LBOARD_PASSWORD=YourPassword123

# Workspace filtering (comma-separated workspace IDs, visible in the board URL)
WORKSPACE_IDS=ws_abc123,ws_def456

# Docker variant only
PORT=3010

# Logging
LOG_LEVEL=info
```

> **Tip:** Your workspace ID appears in the Pix3lBoard URL when you open a board: `https://your-board.vercel.app/workspace/ws_abc123/...`

## Installation

### Option 1: Deploy on Vercel (recommended)

1. Fork or clone the repository and push to GitHub
2. Import in Vercel, set Framework Preset to **Next.js**
3. Add environment variables: `PIX3LBOARD_URL`, `PIX3LBOARD_API_KEY`, `WORKSPACE_IDS`
4. Deploy
5. Register in Claude Code:

```bash
claude mcp add --transport http --scope user pix3lboard -- https://<your-deployment>.vercel.app/api/mcp
```

Or add to Claude.ai via **Settings → Integrations → Add MCP Server**.

### Option 2: Docker (with pix3ltools-deploy)

```bash
git clone https://github.com/Pix3ltools-lab/pix3ltools-deploy.git
cd pix3ltools-deploy
./setup.sh
```

After setup, Pix3lMCP runs on `http://localhost:3010`. Register it in Claude Code:

```bash
claude mcp add --transport http --scope user pix3lboard -- http://localhost:3010/mcp
```

### Option 3: Local development (Vercel variant)

```bash
git clone https://github.com/Pix3ltools-lab/pix3lmcp.git
cd pix3lmcp
npm install
npm run dev:vercel    # http://localhost:3000/api/mcp

claude mcp add --transport http --scope user pix3lboard -- http://localhost:3000/api/mcp
```

### Option 4: Local development (Docker/Express variant)

```bash
npm run dev:docker    # http://localhost:3010/mcp

claude mcp add --transport http --scope user pix3lboard -- http://localhost:3010/mcp
```

## Usage Examples

Once registered, you can ask Claude Code:

- *"Show me all my boards in workspace ws_abc123"*
- *"Create a new card called 'Midjourney portrait' in the 'In Progress' list"*
- *"Move the card 'Logo design' to the 'Done' list"*
- *"Add a prompt to card 'Hero image': a cinematic portrait, golden hour, --ar 16:9 --v 6"*
- *"Archive all cards in the 'Done' list of board 'Project X'"*

## Architecture

```
src/
├── auth.ts          # API Key or Email/Password authentication
├── api.ts           # HTTP client for Pix3lBoard REST API v1
└── tools/
    ├── boards.ts    # Board management tools
    ├── lists.ts     # List management tools
    ├── cards.ts     # Card management tools
    └── index.ts     # Tool registration + list_workspaces

app/api/mcp/         # Vercel: Next.js API route (Streamable HTTP)
server.ts            # Docker: Express entry point
```

## npm Scripts

```bash
npm run dev:vercel    # Next.js dev server (Vercel variant)
npm run dev:docker    # tsx watch (Docker/Express variant)
npm run build:vercel  # Next.js production build
npm run build:docker  # esbuild → dist/server.js
npm run build         # Build both variants
npm run start:vercel  # Start Next.js production server
npm run start:docker  # Start Express production server
npm run lint          # ESLint
```
