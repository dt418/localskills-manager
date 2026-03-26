---
name: localskills-manager
description: Manage skills on localskills.sh - list, view details, delete, update versions. Use when user asks to delete a skill, remove skill, list skills, or manage localskills.
version: 1.0.0
author: dt418
license: MIT
tags:
  - localskills
  - delete
  - manage
  - skill
platforms:
  - opencode
  - claude
  - cursor
  - codex
  - windsurf
  - copilot
tools:
  - Bash
  - Read
---

# Localskills Manager

Manage skills on localskills.sh using the REST API.

## Prerequisites

### Get API Token

```bash
cat ~/.config/localskills/config.json
```

Copy the token from `profiles.default.token`.

## Workflows

### 1. List All Your Skills

```bash
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | {name, slug, publicId, id, visibility, currentVersion}'
```

### 2. Get Skill Details

```bash
# By internal ID
curl -s "https://localskills.sh/api/skills/ID" \
  -H "Authorization: Bearer $TOKEN"

# By public ID
curl -s "https://localskills.sh/api/skills/public/PUBLIC_ID" \
  -H "Authorization: Bearer $TOKEN"
```

### 3. Delete a Skill

```bash
# Using internal ID (format: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
curl -s -X DELETE "https://localskills.sh/api/skills/INTERNAL_ID" \
  -H "Authorization: Bearer $TOKEN"
```

**Response:** `{"success":true}`

### 4. Update Skill (Publish New Version)

```bash
curl -s -X PUT "https://localskills.sh/api/skills/INTERNAL_ID/versions" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "MD file content here",
    "message": "Version update message"
  }'
```

### 5. Get Skill Versions

```bash
curl -s "https://localskills.sh/api/skills/INTERNAL_ID/versions" \
  -H "Authorization: Bearer $TOKEN"
```

### 6. Get Skill Analytics

```bash
curl -s "https://localskills.sh/api/skills/INTERNAL_ID/analytics" \
  -H "Authorization: Bearer $TOKEN"
```

## User Input Required

Ask user for:
1. **API Token** - From `~/.config/localskills/config.json`
2. **Action** - list, view, delete, update
3. **Skill ID** - Internal ID or public ID

## Example: Delete Skill

User wants to delete skill with public ID `M4DUKjLOkn`:

```bash
TOKEN="lsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Step 1: Find internal ID
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | select(.publicId=="M4DUKjLOkn") | {id, name, publicId}'
# Output: {"id":"df7b485a-225d-436c-b218-5c0fc928205c","name":"9router-docker-smoke-test","publicId":"M4DUKjLOkn"}

# Step 2: Delete
curl -s -X DELETE "https://localskills.sh/api/skills/df7b485a-225d-436c-b218-5c0fc928205c" \
  -H "Authorization: Bearer $TOKEN"
# Output: {"success":true}
```

## API Endpoints Summary

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/skills` | List all skills |
| GET | `/api/skills/:id` | Get skill by internal ID |
| GET | `/api/skills/public/:publicId` | Get skill by public ID |
| POST | `/api/skills` | Create new skill |
| PUT | `/api/skills/:id` | Update skill metadata |
| DELETE | `/api/skills/:id` | Delete skill |
| GET | `/api/skills/:id/versions` | List skill versions |
| POST | `/api/skills/:id/versions` | Add new version |
| GET | `/api/skills/:id/analytics` | Get skill analytics |

## Error Handling

| Status | Meaning | Solution |
|--------|---------|----------|
| 401 | Token invalid/expired | Re-run `localskills login` |
| 403 | Not owner of skill | Cannot modify others' skills |
| 404 | Skill not found | Check skill ID |
| 429 | Rate limited | Wait and retry |

## Quick Reference

```bash
# Variables
TOKEN="lsk_your_token_here"
INTERNAL_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
PUBLIC_ID="M4DUKjLOkn"

# List skills
curl -s "https://localskills.sh/api/skills" -H "Authorization: Bearer $TOKEN"

# Delete skill
curl -s -X DELETE "https://localskills.sh/api/skills/$INTERNAL_ID" -H "Authorization: Bearer $TOKEN"

# Get single skill
curl -s "https://localskills.sh/api/skills/$INTERNAL_ID" -H "Authorization: Bearer $TOKEN"
```
