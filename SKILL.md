---
name: localskills-manager
description: Manage skills on localskills.sh - list, view details, delete, update versions. Use when user asks to delete a skill, remove skill, list skills, manage localskills, or run help.
version: 1.1.0
author: dt418
license: MIT
source: https://github.com/dt418/localskills-manager
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

Manage skills on localskills.sh using the REST API. Supports auto-configuration or manual input.

## Quick Start

### Automatic (Recommended)

The skill will automatically:
1. Read token from `~/.config/localskills/config.json`
2. List all your skills
3. Allow actions via natural language

Just tell the agent what you want to do!

### Manual Input

If token not found, provide it when asked.

## Available Actions

Use natural language or commands:

### 1. List All Skills
- "list my skills"
- "show all skills"
- "localskills-manager list"

### 2. View Skill Details
- "show me skill details for XYZ"
- "view skill ABC"
- "localskills-manager view <slug>"

### 3. Delete a Skill
- "delete skill XYZ"
- "remove skill ABC"
- "localskills-manager delete <skill-id>"

### 4. Update Skill (New Version)
- "update skill with new version"
- "publish new version to skill XYZ"
- "localskills-manager update <skill-id> <md-file>"

### 5. Get Analytics
- "show my analytics"
- "what stats do I have?"
- "localskills-manager analytics <skill-id>"

### 6. Help
- "help"
- "show commands"
- "localskills-manager help"

## Auto-Detection

### API Token (Automatic)
```bash
# Reads from ~/.localskills/config.json
# Profile: profiles.default.token
```

### User Input Fallback
If token not found, ask user:
```
Please provide your localskills API token:
1. Run: cat ~/.localskills/config.json
2. Copy the token from profiles.default.token
3. Paste it here
```

## Workflow

### Step 1: Get Token (Auto or Manual)

**Auto-detect:**
```bash
TOKEN=$(cat ~/.localskills/config.json | jq -r '.profiles.default.token')
```

**If not found, ask user for token**

### Step 2: Perform Action

Use natural language or commands from above

### Step 3: Report Result

Format results in tables with status indicators

## Command Examples

### List Skills
```bash
TOKEN="lsk_xxx..."
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | {name, publicId, slug, currentVersion}'
```

### View Single Skill
```bash
# By public ID (slug)
curl -s "https://localskills.sh/api/skills/public/M4DUKjLOkn" \
  -H "Authorization: Bearer $TOKEN"

# By internal ID
curl -s "https://localskills.sh/api/skills/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" \
  -H "Authorization: Bearer $TOKEN"
```

### Delete Skill
```bash
# First find internal ID
curl -s "https://localskills.sh/api/skills" -H "Authorization: Bearer $TOKEN" \
  | jq '.data[] | select(.publicId=="M4DUKjLOkn") | {id, name, publicId}'

# Delete
curl -s -X DELETE "https://localskills.sh/api/skills/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" \
  -H "Authorization: Bearer $TOKEN"
# Response: {"success":true}
```

### Update Skill (New Version)
```bash
CONTENT=$(cat skill.md)
curl -s -X PUT "https://localskills.sh/api/skills/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/versions" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"content\":\"$CONTENT\",\"message\":\"Version update\"}"
```

### Get Analytics
```bash
curl -s "https://localskills.sh/api/skills/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/analytics" \
  -H "Authorization: Bearer $TOKEN"
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

| Error | Solution |
|-------|----------|
| Token not found | Run `cat ~/.localskills/config.json` |
| 401 Unauthorized | Re-run `npx @localskills/cli login` |
| 404 Not Found | Check skill ID/slug is correct |
| 403 Forbidden | You don't own this skill |

## Notes

- Internal ID (uuid) is different from public ID (slug)
- Deleted skills cannot be recovered
- Only skills you own can be modified/deleted
