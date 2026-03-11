---
title: "Pix3lBoard REST API Reference"
description: "Complete reference for the Pix3lBoard REST API v1: authentication, boards, lists, cards, and API keys."
category: "Pix3lBoard"
---

# Pix3lBoard REST API Reference

Pix3lBoard exposes a REST API v1 for external integrations. An interactive Swagger UI is available at `/docs` on any Pix3lBoard instance, and the raw OpenAPI spec at `GET /api/docs`.

## Authentication

Three authentication methods are supported:

### 1. API Key (recommended)
Long-lived credentials, never expire, can be revoked manually.

```
Authorization: Bearer pk_live_XXXXX
```

Create an API key from your profile settings or via the API (see [API Keys](#api-keys) section). The key is shown only once at creation — copy it immediately.

### 2. JWT Token
Short-lived token (7 days), obtained by logging in:

```http
POST /api/auth/token
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "your-password"
}
```

**Response:**
```json
{
  "token": "eyJ...",
  "expires_in": "7d",
  "user": { "id": "...", "email": "user@example.com", "name": "..." }
}
```

Then use it as a Bearer token:
```
Authorization: Bearer eyJ...
```

> **Rate limit:** 5 login attempts per 15 minutes.

### 3. Cookie (web UI only)
Handled automatically by the browser session.

---

## Response Format

| Type | Shape |
|------|-------|
| Single entity | `{ "data": { ...fields } }` |
| Collection | `{ "data": [ ...items ] }` |
| Paginated | `{ "data": [...], "pagination": { "page", "limit", "total", "total_pages" } }` |
| Delete/Archive | `{ "success": true }` |
| Error | `{ "error": "message" }` or `{ "error": "...", "issues": [...] }` |

**HTTP status codes:** 200, 201, 400, 401, 403, 404, 429, 500

---

## Permission Roles

Each board has four roles:

| Role | Permissions |
|------|-------------|
| `owner` | Full control, can delete the board |
| `editor` | Manage board settings, lists, and cards |
| `commenter` | View and comment only |
| `viewer` | Read-only |

---

## Boards

### List boards
```
GET /api/v1/boards
```

**Query params:**
- `workspace_id` — filter by workspace (optional)

**Response:**
```json
{
  "data": [
    {
      "id": "board-id",
      "workspace_id": "workspace-id",
      "name": "My Board",
      "description": null,
      "is_public": false,
      "allowed_card_types": ["task", "bug"],
      "role": "owner",
      "created_at": "2026-03-01T12:00:00.000Z",
      "updated_at": "2026-03-01T12:00:00.000Z"
    }
  ]
}
```

---

### Get board
```
GET /api/v1/boards/{boardId}
```

Returns the board with nested lists and cards.

---

### Create board
```
POST /api/v1/boards
```

```json
{
  "workspace_id": "workspace-id",
  "name": "Board Name",
  "description": "Optional",
  "is_public": false,
  "allowed_card_types": ["task", "bug", "feature"]
}
```

**Required:** `workspace_id`, `name`

---

### Update board
```
PATCH /api/v1/boards/{boardId}
```

All fields optional. Use `null` to clear a field.

---

### Delete board
```
DELETE /api/v1/boards/{boardId}
```

Cascades to lists, cards, and comments. Owner only.

---

## Lists

### List lists
```
GET /api/v1/boards/{boardId}/lists
```

### Create list
```
POST /api/v1/boards/{boardId}/lists
```

```json
{
  "name": "To Do",
  "position": 0,
  "color": "#ff1493"
}
```

**Required:** `name`

### Update list
```
PATCH /api/v1/lists/{listId}
```

### Delete list
```
DELETE /api/v1/lists/{listId}
```

Cascades to cards and comments.

---

## Cards

### List cards
```
GET /api/v1/boards/{boardId}/cards
```

**Query params:**

| Param | Type | Description |
|-------|------|-------------|
| `list_id` | string | Filter by list |
| `is_archived` | boolean | Include archived cards |
| `responsible_user_id` | string | Filter by assigned user |
| `page` | integer | Page number (default: 1) |
| `limit` | integer | Items per page (default: 50, max: 200) |

**Response includes pagination:**
```json
{
  "data": [...],
  "pagination": { "page": 1, "limit": 50, "total": 120, "total_pages": 3 }
}
```

---

### Get card
```
GET /api/v1/cards/{cardId}
```

Returns the full card with comments and attachments.

---

### Create card
```
POST /api/v1/cards
```

**Required:** `list_id`, `title`

**All available fields:**

| Field | Type | Description |
|-------|------|-------------|
| `list_id` | string | Target list |
| `title` | string | Card title (max 500 chars) |
| `description` | string | Markdown description (max 10,000 chars) |
| `type` | string | task, bug, feature, meeting, music, video, image, audio, text |
| `priority` | string | low, medium, high, urgent |
| `severity` | string | low, medium, high, critical |
| `effort` | string | small, medium, large, xlarge |
| `prompt` | string | AI prompt text (max 10,000 chars) |
| `ai_tool` | string | AI tool used (max 100 chars) |
| `rating` | integer | 1–5 stars |
| `tags` | string[] | Max 20 tags, each max 50 chars |
| `due_date` | string | ISO date (YYYY-MM-DD) |
| `links` | string[] | Max 20 URLs |
| `responsible` | string | Assignee name (max 100 chars) |
| `responsible_user_id` | string | Assignee user ID |
| `job_number` | string | Job reference (max 50 chars) |
| `attendees` | string[] | Meeting attendees (max 50) |
| `meeting_date` | string | ISO date |
| `checklist` | object[] | `[{ "id": "1", "text": "Step", "checked": false }]` |
| `thumbnail` | string | Thumbnail URL |
| `wiki_page_id` | string | Linked Pix3lWiki page ID |
| `position` | integer | Auto-calculated if omitted |

---

### Update card
```
PATCH /api/v1/cards/{cardId}
```

All fields optional. Omitted fields are not changed. Set to `null` to clear a field.

---

### Move card
```
PATCH /api/v1/cards/{cardId}/move
```

```json
{
  "list_id": "target-list-id",
  "position": 0
}
```

Handles position recalculation in both source and target lists automatically.

---

### Archive / unarchive card
```
POST /api/v1/cards/{cardId}/archive?action=archive
POST /api/v1/cards/{cardId}/archive?action=unarchive
```

---

### Delete card
```
DELETE /api/v1/cards/{cardId}
```

---

## API Keys

### List API keys
```
GET /api/v1/api-keys
```

Returns all keys for the authenticated user (prefix only, never the full key).

### Create API key
```
POST /api/v1/api-keys
```

```json
{ "name": "pix3lmcp server" }
```

**Response:**
```json
{
  "data": {
    "id": "key-id",
    "name": "pix3lmcp server",
    "key": "pk_live_X7kQm2nRpLwF9vZdYcBtHsEjAo3iUgN4",
    "key_prefix": "pk_live_X7kQ",
    "created_at": "2026-03-01T12:00:00.000Z"
  }
}
```

> The `key` field is shown **only once**. Copy it immediately — it cannot be retrieved later.

**Limit:** 10 API keys per user.

### Delete API key
```
DELETE /api/v1/api-keys/{keyId}
```

---

## Full Example (curl)

```bash
# 1. Get a JWT token
TOKEN=$(curl -s -X POST https://your-board.vercel.app/api/auth/token \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"your-password"}' \
  | jq -r .token)

# 2. Create an API key
curl -s -X POST https://your-board.vercel.app/api/v1/api-keys \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"my-integration"}'

# 3. Use the API key
API_KEY="pk_live_..."

# List boards
curl -s https://your-board.vercel.app/api/v1/boards \
  -H "Authorization: Bearer $API_KEY"

# Create a card
curl -s -X POST https://your-board.vercel.app/api/v1/cards \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "list_id": "list-id",
    "title": "New feature",
    "type": "feature",
    "priority": "high",
    "tags": ["api", "v1"]
  }'
```
