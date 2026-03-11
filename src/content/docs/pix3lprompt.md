---
title: "Pix3lPrompt: AI Prompt Editor"
description: "An intelligent prompt editor for AI image, video, and audio generators. Craft, optimize, and manage prompts — 100% client-side, no account required."
category: "Pix3lPrompt"
---

# Pix3lPrompt

Pix3lPrompt is an intelligent prompt editor for AI image, video, and audio generators. Build and optimize prompts for Midjourney, Flux, Stable Diffusion, DALL-E, Kling, Runway, Suno, Udio, and more — directly in your browser. All data is stored locally in IndexedDB: no backend, no account required.

**Repository:** [github.com/Pix3ltools-lab/pix3lprompt](https://github.com/Pix3ltools-lab/pix3lprompt)

## Tech Stack

- **Framework:** Next.js (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS 4 + shadcn/ui
- **State Management:** Zustand
- **Storage:** IndexedDB via Dexie.js (100% client-side)
- **Icons:** Lucide React
- **Theme:** next-themes (dark/light mode)
- **Analytics:** Vercel Analytics

## Features

### Multi-model Prompt Builder
Model-aware prompt assembly with the correct syntax for each generator:
- **Midjourney:** `--ar`, `--v`, `--no` parameters and parameter-aware formatting
- **Flux / Stable Diffusion:** Natural language prompts
- **DALL-E:** OpenAI-specific formatting
- **Suno / Udio:** Audio prompt syntax for music and sound generation
- **Kling / Runway:** Video generation prompt formatting

### Visual Editor
- Subject field as the primary input
- 20 style chips (cinematic, photorealistic, watercolor, etc.)
- 14 lighting presets
- Aspect ratio picker
- Negative prompt field
- Color-coded live preview — each segment type highlighted differently

### AI-Powered Optimization
Connect to an AI provider to refine and improve your prompts:
- **OpenRouter** (multi-model)
- **OpenAI**
- **Anthropic**
- **LM Studio** (local models)

Provider configuration is handled in the Settings page — no environment variables required.

### Prompt History
- Save prompts with metadata: model, style, rating (1–5 stars), tags, favorites
- Search and filter saved prompts
- Reload any saved prompt into the editor
- Quick-start templates

### Progressive Web App (PWA)
- Installable on desktop and mobile as a standalone app
- Offline support — works without an internet connection

### Layout
- **Desktop:** 3-column layout (History | Editor | Templates)
- **Mobile:** Tab-based layout with bottom navigation

## Environment Variables

No environment variables are required for basic usage. AI provider credentials (API keys) are entered directly in the app's Settings page and stored locally in the browser.

## Running Locally

```bash
git clone https://github.com/Pix3ltools-lab/pix3lprompt.git
cd pix3lprompt

npm install
npm run dev    # http://localhost:3000
```

## Deployment

### Vercel

1. Push to GitHub
2. Import the repository in Vercel
3. Set Framework Preset to **Next.js**
4. Deploy — no environment variables needed

### Docker

```bash
git clone https://github.com/Pix3ltools-lab/pix3ltools-deploy.git
cd pix3ltools-deploy
./setup.sh
# Pix3lPrompt runs on http://localhost:3002
```

The Docker image uses a multi-stage build (node:20-alpine), runs as non-root user (uid 1000), exposes port 3002. No database or storage volumes required.

## npm Scripts

```bash
npm run dev    # Development server
npm run build  # Production build
npm run start  # Start production server
npm run lint   # ESLint
```
