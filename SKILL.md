---
name: localskills-delete
description: Delete a skill from localskills.sh using API. Use when user asks to delete a skill, remove skill, or uninstall skill from localskills.
version: 1.0.0
author: dt418
license: MIT
tags:
  - localskills
  - delete
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
---

# Localskills Delete Skill

Delete a skill from localskills.sh using the API.

## Prerequisites

1. Login to localskills:
```bash
npx @localskills/cli login
```

2. Get API token from config:
```bash
cat ~/.config/localskills/config.json
```
Copy the `token` value from the profile.

## Workflow

### 1. Get User Input

Ask user for:
- **Skill ID** (required): The internal ID of the skill to delete (format: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`)
- Or **Public ID** (optional): The public slug/ID like `M4DUKjLOkn`

### 2. Get API Token

```bash
cat ~/.config/localskills/config.json
```

Extract the token from `profiles.default.token`.

### 3. Find Skill ID (if only Public ID provided)

If user only provides public ID (like `M4DUKjLOkn`), first find the internal ID:

```bash
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | select(.publicId=="PUBLIC_ID") | .id'
```

### 4. Delete the Skill

```bash
# Using internal ID
curl -s -X DELETE "https://localskills.sh/api/skills/SKILL_ID" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json"
```

### 5. Confirm Deletion

If successful, the response will be:
```json
{"success":true}
```

## Example

User wants to delete skill with public ID `M4DUKjLOkn`:

```bash
# Step 1: Get token
TOKEN="lsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Step 2: Find internal ID
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | select(.publicId=="M4DUKjLOkn")'
# Output: {"id":"df7b485a-225d-436c-b218-5c0fc928205c","publicId":"M4DUKjLOkn",...}

# Step 3: Delete
curl -s -X DELETE "https://localskills.sh/api/skills/df7b485a-225d-436c-b218-5c0fc928205c" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json"
# Output: {"success":true}
```

## List All Your Skills

To see all your skills with their IDs:

```bash
curl -s "https://localskills.sh/api/skills" \
  -H "Authorization: Bearer $TOKEN" | jq '.data[] | {id: .id, publicId: .publicId, name: .name, slug: .slug}'
```

## Error Handling

- **401 Unauthorized**: Token is invalid or expired. Re-run `localskills login`
- **404 Not Found**: Skill ID is incorrect
- **403 Forbidden**: You don't own this skill

## Notes

- Only skills you own can be deleted
- Deleted skills cannot be recovered
- The public ID (slug) is different from the internal ID
