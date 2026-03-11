---
title: "Pix3lBoard: Kanban for AI Creators"
description: "How Pix3lBoard extends the classic Kanban board with AI-specific fields: prompts, ratings, job numbers, Midjourney/Suno tracking, and a full REST API."
category: "Pix3lBoard"
---

# Pix3lBoard

Pix3lBoard is a modern Kanban board designed for AI creators and project managers. It extends the classic Kanban workflow with fields specific to AI generation workflows: prompt tracking, AI tool assignment, ratings, job numbers, and more.

**Repository:** [github.com/Pix3ltools-lab/pix3lboard](https://github.com/Pix3ltools-lab/pix3lboard)

## Tech Stack

- **Framework:** Next.js 14 (App Router)
- **Language:** TypeScript (strict mode)
- **Database:** Turso (LibSQL / SQLite)
- **Storage:** Vercel Blob (cloud) or local filesystem (Docker)
- **Authentication:** Custom JWT with bcryptjs
- **Styling:** Tailwind CSS
- **Drag & Drop:** @dnd-kit
- **Charts:** Recharts
- **Validation:** Zod
- **Logging:** Pino

## Features

### Boards and Cards
- Workspaces, boards, and lists with full drag-and-drop support
- Multiple card types: Task, Bug, Feature, Meeting, Music, Video, Image, Audio, Text
- Card fields: title, description, type, priority, severity, effort, due date, responsible person, job number
- AI-specific fields: prompt, AI tool used, rating (1–5 stars), tags
- Checklists, comments, activity log, file attachments (max 10 MB), thumbnails

### Views
- **Kanban board** — classic column view with drag & drop
- **Calendar view** — cards organized by due date
- **Analytics dashboard** — charts for cards per list, card type distribution, throughput, lead time

### Collaboration
- Share boards with users using 4 roles: Owner, Editor, Commenter, Viewer
- Public boards (read-only link sharing)
- Bell notifications for assignments, comments, and due dates

### Search and Filters
- Full-text search powered by FTS5
- Filters: tags, job number, responsible person
- Quick filters: My cards, Due soon, Overdue, High priority

### Admin Panel
- User management and approval workflow
- Password reset
- Database backup and restore
- Storage cleanup

### REST API v1
- Full CRUD for boards, lists, and cards
- Bearer token authentication
- Swagger UI at `/docs`

## Environment Variables

```env
# App
NEXT_PUBLIC_APP_NAME=pix3lboard
NEXT_PUBLIC_APP_VERSION=3.0.1
NEXT_PUBLIC_APP_URL=https://your-deployment.vercel.app

# Database
TURSO_DATABASE_URL=libsql://your-database.turso.io
TURSO_AUTH_TOKEN=your-auth-token

# Authentication
JWT_SECRET=your-random-secret-key-at-least-32-characters-long

# Storage (default: Vercel Blob)
STORAGE_PROVIDER=local          # or 'vercel'
LOCAL_STORAGE_PATH=/data/blob-storage
BLOB_READ_WRITE_TOKEN=your-vercel-blob-token

# Integrations
NEXT_PUBLIC_PIX3LWIKI_URL=https://your-wiki.vercel.app
PIX3LPROMPT_URL=https://your-prompt.vercel.app

# Logging
LOG_LEVEL=info
```

## Running Locally

```bash
git clone https://github.com/Pix3ltools-lab/pix3lboard.git
cd pix3lboard

# Create Turso database
curl -sSfL https://get.tur.so/install.sh | bash
turso auth login
turso db create pix3lboard
turso db show pix3lboard --url
turso db tokens create pix3lboard

# Setup environment
cp .env.example .env.local
# Edit .env.local with your credentials

# Install dependencies and initialize database
npm install
npm run db:setup

# Start development server
npm run dev        # http://localhost:3000
npm run dev:pretty # with pretty-printed logs
```

## Deployment

### Vercel

1. Push to GitHub
2. Import the repository in Vercel
3. Set Framework Preset to **Next.js**
4. Add environment variables: `TURSO_DATABASE_URL`, `TURSO_AUTH_TOKEN`, `JWT_SECRET`, `BLOB_READ_WRITE_TOKEN`
5. Deploy

### Docker

```bash
git clone https://github.com/Pix3ltools-lab/pix3ltools-deploy.git
cd pix3ltools-deploy
./setup.sh
```

The Docker image uses a multi-stage build (node:20-alpine), runs as non-root user (uid 1000), exposes port 3000, and stores file attachments at `/data/blob-storage`.

## npm Scripts

```bash
npm run dev           # Development server
npm run dev:pretty    # Dev with pretty-printed logs
npm run build         # Production build
npm run start         # Start production server
npm run lint          # ESLint
npm run type-check    # TypeScript type checking
npm run db:setup      # Initialize database tables
npm run test:unit     # Vitest unit tests
npm run test:e2e      # Playwright E2E tests
```
