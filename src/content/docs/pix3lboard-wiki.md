---
title: "Pix3lBoard + Pix3lWiki Integration"
description: "How to link wiki pages to boards and cards, navigate between the two apps, and set up cross-app integration."
category: "Pix3lBoard"
---

# Pix3lBoard + Pix3lWiki Integration

Pix3lBoard and Pix3lWiki are designed to work together. They share the same database and authentication, allowing you to link wiki documentation directly to boards and cards.

## How It Works

### Shared Database

Both apps use the same Turso/sqld database. Pix3lWiki tables (`wiki_pages`, `wiki_versions`, `wiki_categories`) live alongside Pix3lBoard tables in the same database. This makes cross-references direct and efficient.

### Shared Authentication

Both apps use the same `JWT_SECRET`. A user logged into Pix3lBoard is automatically authenticated in Pix3lWiki — no second login required, as long as both apps share the same secret.

---

## Setup

In Pix3lBoard, set the Pix3lWiki URL:

```env
NEXT_PUBLIC_PIX3LWIKI_URL=https://your-wiki.vercel.app
```

In Pix3lWiki, set the Pix3lBoard URL:

```env
NEXT_PUBLIC_PIX3LBOARD_URL=https://your-board.vercel.app
```

> In Docker deployments using pix3ltools-deploy, these variables are configured automatically.

The Pix3lBoard URL is validated at runtime — it must be a valid HTTPS URL. In development (localhost), it falls back gracefully.

---

## Linking a Card to a Wiki Page

Every card can have a linked wiki page. There are two ways to create the link:

### From a card in Pix3lBoard

1. Open a card
2. Click the **Wiki** button (book icon) in the card actions
3. Pix3lWiki opens with the new page form pre-filled with the board and card context:
   ```
   https://your-wiki.vercel.app/wiki/new?board={boardId}&card={cardId}
   ```
4. Write and save the wiki page
5. The card now shows a **Wiki badge** (book icon) in the board view

### Via the REST API

Set `wiki_page_id` when creating or updating a card:

```bash
PATCH /api/v1/cards/{cardId}
{
  "wiki_page_id": "wiki-page-id"
}
```

---

## Navigation

### From Pix3lBoard → Pix3lWiki

- **Header link:** A "Pix3lWiki" link in the Pix3lBoard navigation bar opens your wiki in a new tab
- **Card wiki button:** Opens the linked wiki page or the new page form with board/card context

### From Pix3lWiki → Pix3lBoard

- Wiki pages created via a card link know their board and card origin
- Navigation back to the originating card is available from the wiki page

### Wiki Badge on Cards

When a card has a linked wiki page (`wiki_page_id` is set), a **book icon** appears on the card in the board view. This gives a quick visual indicator that documentation exists for that card.

---

## Use Cases

**Project documentation:** Create a board for a project, add cards for each task, and link each card to a wiki page with detailed specs, research notes, or meeting summaries.

**AI prompt library:** Track prompts in Pix3lBoard cards (with ratings and AI tool info) and link each to a wiki page explaining the workflow or results.

**Bug tracking:** Log bugs as cards, then link each to a wiki page documenting the root cause analysis and fix details.

**Knowledge base:** Use Pix3lWiki as the documentation layer for your team, with every wiki page linked back to the relevant board card for traceability.

---

## Configuration Reference

| Variable | App | Description |
|----------|-----|-------------|
| `NEXT_PUBLIC_PIX3LWIKI_URL` | Pix3lBoard | URL of your Pix3lWiki instance |
| `NEXT_PUBLIC_PIX3LBOARD_URL` | Pix3lWiki | URL of your Pix3lBoard instance |
| `JWT_SECRET` | Both | Must be identical in both apps |
| `TURSO_DATABASE_URL` | Both | Must point to the same database |
| `TURSO_AUTH_TOKEN` | Both | Must use the same database credentials |
