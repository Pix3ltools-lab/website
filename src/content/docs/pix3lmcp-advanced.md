---
title: "Pix3lMCP: Advanced Usage and Tool Reference"
description: "Complete parameter reference for all 17 Pix3lMCP tools, with workflow examples for managing Pix3lBoard from Claude Code."
category: "Pix3lMCP"
---

# Pix3lMCP: Advanced Usage and Tool Reference

This page covers all available tool parameters in detail and provides real workflow examples for managing Pix3lBoard directly from Claude Code.

For installation and setup, see the [Pix3lMCP guide](/docs/pix3lmcp).

---

## Tool Parameter Reference

### Workspaces

#### `list_workspaces`
List all accessible workspaces.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| *(none)* | | | |

---

### Boards

#### `list_boards`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workspace_id` | string | ✓ | Workspace ID |

#### `get_board`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |

Returns the board with all lists and cards.

#### `create_board`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workspace_id` | string | ✓ | Workspace ID |
| `name` | string | ✓ | Board name |
| `description` | string | | Board description |

#### `update_board`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |
| `name` | string | | New name |
| `description` | string | | New description |

#### `delete_board`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |

---

### Lists

#### `list_lists`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |

#### `create_list`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |
| `name` | string | ✓ | List name |
| `position` | number | | Position (auto if omitted) |

#### `update_list`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `list_id` | string | ✓ | List ID |
| `name` | string | | New name |
| `position` | number | | New position |

#### `delete_list`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `list_id` | string | ✓ | List ID |

---

### Cards

#### `list_cards`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `board_id` | string | ✓ | Board ID |

#### `get_card`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `card_id` | string | ✓ | Card ID |

Returns full card with comments and attachments.

#### `create_card`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `list_id` | string | ✓ | Target list ID |
| `title` | string | ✓ | Card title |
| `description` | string | | Card description (Markdown) |
| `type` | string | | task, bug, feature, meeting, text, image, music, video, audio (always lowercase) |
| `priority` | string | | low, medium, high, urgent |
| `severity` | string | | low, medium, high, critical |
| `effort` | string | | small, medium, large, xlarge |
| `tags` | string[] | | Array of tag strings |
| `due_date` | string | | ISO 8601 date (YYYY-MM-DD) |
| `responsible` | string | | Assignee name |
| `prompt` | string | | AI prompt text |
| `ai_tool` | string | | AI tool used (e.g. Midjourney, Suno) |
| `job_number` | string | | Job reference (e.g. C-26-0001) |
| `rating` | number | | 1–5 stars |
| `links` | string[] | | Array of URLs |
| `checklist` | object[] | | `[{"id":"1","text":"Step","checked":false}]` |

#### `update_card`

Same parameters as `create_card`, but `list_id` and `title` are optional. Omitted fields are not changed.

#### `move_card`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `card_id` | string | ✓ | Card ID |
| `list_id` | string | ✓ | Target list ID |
| `position` | number | ✓ | Target position (0 = top) |

#### `archive_card`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `card_id` | string | ✓ | Card ID |

#### `delete_card`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `card_id` | string | ✓ | Card ID |

---

## Workflow Examples

### Set up a new project board

> *"Create a new board called 'Website Redesign' in my workspace, then add three lists: Backlog, In Progress, and Done."*

Claude will:
1. Call `list_workspaces` to find your workspace ID
2. Call `create_board` with name "Website Redesign"
3. Call `create_list` three times for each column

---

### Add a batch of AI generation tasks

> *"In the 'Midjourney' board, add 5 cards to the Backlog list for these prompts: [your list of prompts]. Set type to 'image' and ai_tool to 'Midjourney'."*

Claude will call `create_card` for each item, setting `type: "image"` and `ai_tool: "Midjourney"` on each.

---

### Review and rate completed work

> *"List all cards in the Done list of board 'Project X'. For each one, show me the prompt and current rating."*

Claude will:
1. Call `get_board` to find the lists
2. Call `list_cards` filtering by the Done list
3. Display the `prompt` and `rating` fields for each card

---

### Move cards between lists

> *"Move all cards tagged 'urgent' from Backlog to In Progress."*

Claude will:
1. Call `list_cards` to find matching cards
2. Call `move_card` for each one targeting the In Progress list

---

### Archive completed cards

> *"Archive all cards in the Done list that have a rating of 4 or 5 stars."*

Claude will list the cards, filter by rating, and call `archive_card` for each matching card.

---

### Track a music generation session

> *"Create a card for a Suno track: title 'Lo-fi study beats', type music, prompt 'calm lo-fi hip hop, piano, light rain, 85bpm', ai_tool Suno, rating 4."*

```
create_card:
  list_id: <your list>
  title: "Lo-fi study beats"
  type: "music"
  prompt: "calm lo-fi hip hop, piano, light rain, 85bpm"
  ai_tool: "Suno"
  rating: 4
```

---

## Tips

- **Find IDs:** Use `list_workspaces` → `list_boards` → `list_lists` to navigate the hierarchy and get the IDs you need
- **Card types are case-sensitive:** Always use lowercase (`task`, not `Task`)
- **Checklist format:** Pass as a JSON array: `[{"id":"1","text":"Review copy","checked":false}]`
- **Ratings:** Must be an integer between 1 and 5
- **Links:** Must be valid URLs (include `https://`)
