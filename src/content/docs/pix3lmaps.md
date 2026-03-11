---
title: "Pix3lMaps"
description: "Privacy-focused mind-mapping tool for content creators. Design maps with templates, custom nodes, and export as PNG or JSON — all data stays in your browser."
category: "Web Tools"
---

# Pix3lMaps

Pix3lMaps is a privacy-focused mind-mapping tool for content creators. Design mind maps with templates, custom nodes, and flexible layouts — then export as PNG or JSON. All data is stored locally in IndexedDB: nothing is sent to any server.

**Repository:** [github.com/Pix3ltools-lab/pix3lmaps](https://github.com/Pix3ltools-lab/pix3lmaps)

## Tech Stack

- **Framework:** Next.js 16 (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS 4
- **Graph rendering:** @xyflow/react (React Flow)
- **Storage:** IndexedDB via Dexie.js (100% client-side)
- **Export:** html-to-image (PNG export)
- **State management:** Zustand

## Features

- **Visual mind map editor:** Drag, connect, and arrange nodes freely using React Flow
- **Custom nodes:** Create nodes with labels, colors, and styles
- **Templates:** Quick-start templates to jumpstart your map
- **Export:**
  - PNG — full-resolution image of your map
  - JSON — portable format for backup or sharing
- **Import:** Load maps from JSON files
- **Privacy-first:** All data stored in IndexedDB in the browser — no account, no server
- **Dark mode:** Default dark theme

## Running Locally

```bash
git clone https://github.com/Pix3ltools-lab/pix3lmaps.git
cd pix3lmaps

npm install
npm run dev    # http://localhost:3000
```

## Deployment

### Vercel

1. Push to GitHub
2. Import the repository in Vercel
3. Set Framework Preset to **Next.js**
4. No environment variables needed
5. Deploy

```bash
npm run build    # production build
npm run start    # start production server
```

## npm Scripts

```bash
npm run dev    # Development server
npm run build  # Production build
npm run start  # Start production server
npm run lint   # ESLint
```
