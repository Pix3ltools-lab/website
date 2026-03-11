---
title: "Pix3lCover"
description: "Web application for creating professional YouTube thumbnails. Design eye-catching covers with templates, custom text, and badge overlays."
category: "Web Tools"
---

# Pix3lCover

Pix3lCover is a web application for creating professional YouTube thumbnails. Design eye-catching covers directly in your browser using templates, custom text, font controls, and badge overlays — then export as JPG or PNG.

**Repository:** [github.com/Pix3ltools-lab/pix3lcover](https://github.com/Pix3ltools-lab/pix3lcover)

## Tech Stack

- **Framework:** React 18
- **Build tool:** Vite 6
- **Styling:** Tailwind CSS 3.4
- **Canvas:** Fabric.js 5.3
- **Background removal:** @imgly/background-removal
- **Typography:** Google Fonts

## Features

- **Templates:** Multiple pre-built thumbnail templates for 16:9 and 9:16 formats
- **Canvas editor:** Drag, resize, and position elements with Fabric.js
- **Text editing:** Title and subtitle with font family, size, weight, and color controls
- **Badge system:** Overlay badges in multiple styles and positions
- **Image upload:** Drag & drop, file picker, or paste from clipboard
- **Background removal:** Remove backgrounds from uploaded images directly in the browser
- **Export:** Download as JPG or PNG
- **Save/Load:** Project persistence via localStorage — your work is saved between sessions
- **No account required:** 100% client-side, no backend

## Running Locally

```bash
git clone https://github.com/Pix3ltools-lab/pix3lcover.git
cd pix3lcover

npm install
npm run dev    # http://localhost:5173
```

## Deployment

### Vercel

1. Push to GitHub
2. Import the repository in Vercel
3. No environment variables needed
4. Deploy

```bash
npm run build    # production build
npm run preview  # preview production build locally
```

## Project Structure

```
src/
├── components/
│   ├── Canvas/     # Canvas rendering and interaction
│   └── Sidebar/    # Sidebar panels (text, images, templates)
├── utils/
│   ├── exportUtils.js    # JPG/PNG export
│   └── storageUtils.js   # localStorage save/load
├── data/
│   ├── templates.js      # Template definitions
│   └── fonts.js          # Google Fonts configuration
└── styles/               # Global styles
```

## npm Scripts

```bash
npm run dev      # Development server (port 5173)
npm run build    # Production build
npm run preview  # Preview production build
npm run lint     # ESLint
```
