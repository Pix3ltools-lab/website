---
title: "Pix3lWiki: Knowledge Base for Your Projects"
description: "How to use Pix3lWiki to create and organize documentation with Markdown, categories, version history, and Pix3lBoard linking."
category: "Pix3lWiki"
---

# Pix3lWiki

Pix3lWiki is a wiki and knowledge base companion for Pix3lBoard. It shares the same database and authentication, allowing users to write rich Markdown documentation linked to boards and cards.

**Repository:** [github.com/Pix3ltools-lab/pix3lwiki](https://github.com/Pix3ltools-lab/pix3lwiki)

## Tech Stack

- **Framework:** Next.js 15 (App Router)
- **Language:** TypeScript (strict mode)
- **Database:** Turso (LibSQL / SQLite) — shared with Pix3lBoard
- **Authentication:** JWT with bcryptjs — shared with Pix3lBoard
- **Markdown:** react-markdown, remark-gfm, rehype-highlight, rehype-slug
- **Styling:** Tailwind CSS
- **Validation:** Zod
- **Logging:** Pino

## Features

### Pages
- Full Markdown support: GitHub Flavored Markdown, syntax highlighting, tables, headings, lists
- Auto-generated table of contents from headings
- Page visibility: Public, Draft, or Archived
- Version history: all edits tracked with optional change summaries and one-click restore

### Organization
- **Categories:** color-coded, with slug support
- **Tags:** flexible cross-category labeling
- **Full-text search:** search across titles and content with category filters

### Editor
- Split-pane editor with live Markdown preview
- Toolbar with common formatting shortcuts
- Real-time rendering

### Pix3lBoard Integration
- Link wiki pages to specific boards and cards
- Context navigation: jump from a card in Pix3lBoard to the related wiki page and back

### Admin
- Page and category management
- Export all pages as Markdown ZIP
- Database backup and restore

## Environment Variables

```env
# Database (same as Pix3lBoard)
TURSO_DATABASE_URL=libsql://your-database.turso.io
TURSO_AUTH_TOKEN=your-auth-token

# Authentication (same JWT_SECRET as Pix3lBoard for token compatibility)
JWT_SECRET=your-random-secret-key-at-least-32-characters-long

# App
NEXT_PUBLIC_APP_VERSION=1.5.0

# Integration
NEXT_PUBLIC_PIX3LBOARD_URL=https://your-board.vercel.app

# Logging
LOG_LEVEL=info
```

> **Important:** `JWT_SECRET` must be identical to the one used by Pix3lBoard so that login tokens are valid across both apps.

## Running Locally

```bash
git clone https://github.com/Pix3ltools-lab/pix3lwiki.git
cd pix3lwiki

# Use the same Turso database as Pix3lBoard
cp .env.example .env
# Edit .env with the same credentials used for Pix3lBoard

npm install
npm run db:setup

npm run dev        # http://localhost:3001
npm run dev:pretty # with pretty-printed logs
```

## Deployment

### Vercel

1. Push to GitHub
2. Import the repository in Vercel
3. Set Framework Preset to **Next.js**
4. Add environment variables: `TURSO_DATABASE_URL`, `TURSO_AUTH_TOKEN`, `JWT_SECRET`, `NEXT_PUBLIC_PIX3LBOARD_URL`
5. Deploy

### Docker

```bash
git clone https://github.com/Pix3ltools-lab/pix3ltools-deploy.git
cd pix3ltools-deploy
./setup.sh
# Pix3lWiki runs on http://localhost:3001
```

The Docker image uses a multi-stage build (node:20-alpine), runs as non-root user (uid 1000), exposes port 3001.

## npm Scripts

```bash
npm run dev              # Development server (port 3001)
npm run dev:pretty       # Dev with pretty-printed logs
npm run build            # Production build
npm run start            # Start production server
npm run lint             # ESLint
npm run type-check       # TypeScript type checking
npm run db:setup         # Initialize database tables
npm run test:unit        # Vitest unit tests
npm run test:e2e         # Playwright E2E tests
npm run test:e2e:ui      # Playwright interactive UI
npm run test:e2e:headed  # E2E with visible browser
```
