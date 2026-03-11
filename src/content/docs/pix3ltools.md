---
title: "Introduction to Pix3lTools"
description: "An overview of the Pix3lTools ecosystem: what it is, what problems it solves, and how the tools work together."
category: "Getting Started"
---

# Introduction to Pix3lTools

Pix3lTools is an open-source ecosystem of specialized tools built for AI creators, developers, and content creators. It covers the full workflow — from managing creative projects and organizing knowledge, to editing AI prompts and integrating with AI assistants.

All projects are maintained under the [Pix3ltools-lab](https://github.com/Pix3ltools-lab) GitHub organization and are designed to work independently or as a unified stack.

## Projects

| Project | Description | Stack |
|---------|-------------|-------|
| **Pix3lBoard** | Kanban board for AI creators | TypeScript / Next.js |
| **Pix3lWiki** | Wiki and knowledge base companion | TypeScript / Next.js |
| **Pix3lPrompt** | AI prompt editor (PWA) | TypeScript / Next.js |
| **Pix3lMCP** | MCP server for Claude Code | TypeScript / MCP |
| **Pix3lForge** | Cross-platform desktop image editor | C++ / Qt |
| **Pix3lSense** | AI-powered image analysis tool | C++ / Qt |
| **Pix3lFrames** | High-performance video frame extractor | C++ / Qt |
| **Pix3lIconv** | Image format converter (PNG/JPEG/SVG → ICO) | C++ / Qt |
| **Pix3lCover** | YouTube thumbnail creator | JavaScript |
| **Pix3lMaps** | Privacy-focused mind-mapping tool | TypeScript / Next.js |

## How the Tools Work Together

The web stack (Pix3lBoard, Pix3lWiki, Pix3lPrompt, Pix3lMCP) is designed for integration:

- **Pix3lBoard** and **Pix3lWiki** share the same database and authentication — users logged into one app are recognized by the other.
- **Pix3lWiki** pages can be linked to Pix3lBoard boards and cards for contextual documentation.
- **Pix3lMCP** connects to Pix3lBoard via its REST API, exposing it as tools inside Claude Code and Claude.ai.
- **Pix3lPrompt** is standalone (no backend required) but integrates with Pix3lBoard for prompt tracking.

## Deployment Options

Each project can be deployed independently on **Vercel** (recommended for cloud) or as a unified stack via **Docker Compose** using [pix3ltools-deploy](https://github.com/Pix3ltools-lab/pix3ltools-deploy).

## Tech Stack Overview

- **Language:** TypeScript (strict mode) for all web projects
- **Framework:** Next.js (App Router)
- **Database:** Turso / LibSQL (SQLite-compatible) — shared between Pix3lBoard and Pix3lWiki
- **Authentication:** Custom JWT with bcryptjs
- **Validation:** Zod schema validation on all inputs
- **Logging:** Structured JSON logging via Pino
- **Containers:** Docker images published to GitHub Container Registry (`ghcr.io/pix3ltools-lab/`)
