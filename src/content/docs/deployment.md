---
title: "Self-hosting with Docker"
description: "Deploy the full Pix3lTools stack — Pix3lBoard, Pix3lWiki, Pix3lPrompt, and Pix3lMCP — on your own server using Docker Compose."
category: "Getting Started"
---

# Self-hosting with Docker

The [pix3ltools-deploy](https://github.com/Pix3ltools-lab/pix3ltools-deploy) repository provides a Docker Compose stack for running the entire Pix3lTools ecosystem on your own server.

## Services

| Service | Port | Description |
|---------|------|-------------|
| **pix3lboard** | 3000 | Kanban board |
| **pix3lwiki** | 3001 | Wiki documentation |
| **pix3lprompt** | 3002 | AI prompt editor |
| **pix3lmcp** | 3010 | MCP server for Claude |
| **sqld** | 8080 | LibSQL database |
| **watchtower** | — | Image update monitor |
| **traefik** | 80/443 | HTTPS reverse proxy (VPS only) |

Docker images are published to GitHub Container Registry:
```
ghcr.io/pix3ltools-lab/pix3lboard:latest
ghcr.io/pix3ltools-lab/pix3lwiki:latest
ghcr.io/pix3ltools-lab/pix3lprompt:latest
ghcr.io/pix3ltools-lab/pix3lmcp:latest
```

## Prerequisites

- Docker and Docker Compose installed
- Ports 3000–3002, 3010, and 8080 available (or 80/443 for VPS with HTTPS)

## Quick Start (Local / WSL / macOS / Linux)

```bash
git clone https://github.com/Pix3ltools-lab/pix3ltools-deploy.git
cd pix3ltools-deploy
./setup.sh
```

The `setup.sh` script:
1. Checks that Docker is installed and running
2. Generates a `.env` file with a secure `JWT_SECRET`
3. Starts all containers
4. Initializes the database and creates the admin account

After setup, access the apps at:
- Pix3lBoard: `http://localhost:3000`
- Pix3lWiki: `http://localhost:3001`
- Pix3lPrompt: `http://localhost:3002`
- Pix3lMCP: `http://localhost:3010`

## Environment Variables

Create a `.env` file (or copy `.env.example`):

```env
# Required
JWT_SECRET=your-secret-key-minimum-32-characters-long

# Pix3lMCP integration
PIX3LBOARD_MCP_EMAIL=admin@example.com
PIX3LBOARD_MCP_PASSWORD=YourPassword123
PIX3LBOARD_MCP_WORKSPACE_IDS=    # configure after first login

# Optional
LOG_LEVEL=info
```

## Production VPS with HTTPS

For a production server with HTTPS and Let's Encrypt certificates:

```bash
./setup.sh              # initial setup
./setup-https.sh        # configure Traefik + Let's Encrypt
sudo ./setup-fail2ban.sh  # brute-force protection (optional)
```

`setup-https.sh` will ask for:
- A custom domain (or sslip.io for IP-based HTTPS)
- One subdomain per app
- An email address for Let's Encrypt notifications

## Manual Setup

If you prefer to configure manually:

```bash
cp .env.example .env
# Edit .env with a strong JWT_SECRET (32+ characters)

docker compose up -d
```

Then initialize the databases:

```bash
# Pix3lBoard database
git clone https://github.com/Pix3ltools-lab/pix3lboard.git /tmp/pix3lboard
TURSO_DATABASE_URL=http://localhost:8080 TURSO_AUTH_TOKEN=dummy \
  E2E_USER_EMAIL=admin@example.com E2E_USER_PASSWORD=YourPassword123 \
  bash /tmp/pix3lboard/scripts/db-init.sh

# Pix3lWiki database
git clone https://github.com/Pix3ltools-lab/pix3lwiki.git /tmp/pix3lwiki
source .env
TURSO_DATABASE_URL=http://localhost:8080 TURSO_AUTH_TOKEN=dummy JWT_SECRET="$JWT_SECRET" \
  E2E_USER_EMAIL=admin@example.com E2E_USER_PASSWORD=YourPassword123 \
  bash /tmp/pix3lwiki/scripts/db-init.sh
```

## Updating

```bash
docker compose pull
docker compose up -d
```

## Backup

```bash
# Database
docker compose cp sqld:/var/lib/sqld ./backup-db

# File attachments (Pix3lBoard thumbnails and uploads)
docker compose cp pix3lboard:/data/blob-storage ./backup-blobs
```

## Deploying on Vercel

Each project can also be deployed independently on Vercel.

### Prerequisites
- A [Turso](https://turso.tech) account (free tier available) for the database
- A Vercel account

### Pix3lBoard / Pix3lWiki

```bash
# Install Turso CLI and create a database
curl -sSfL https://get.tur.so/install.sh | bash
turso auth login
turso db create pix3lboard
turso db show pix3lboard --url     # → TURSO_DATABASE_URL
turso db tokens create pix3lboard  # → TURSO_AUTH_TOKEN
```

In the Vercel dashboard:
1. Import the repository
2. Set **Framework Preset** to **Astro** (for the website) or **Next.js** (for the apps)
3. Add environment variables: `TURSO_DATABASE_URL`, `TURSO_AUTH_TOKEN`, `JWT_SECRET`
4. Deploy

### Pix3lPrompt

No environment variables required. Import the repository in Vercel and deploy — zero configuration needed.

### Pix3lMCP

```bash
# In Vercel dashboard
# Add: PIX3LBOARD_URL, PIX3LBOARD_API_KEY (or EMAIL+PASSWORD), WORKSPACE_IDS
```

After deploy, register in Claude Code:
```bash
claude mcp add --transport http --scope user pix3lboard -- https://<your-deployment>.vercel.app/api/mcp
```
