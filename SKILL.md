---
name: bot-email
description: Professional email infrastructure for autonomous agents. Create and manage bot email accounts on BotEmail.ai for testing, automation, and email workflows. Supports proactive inbox monitoring via heartbeat — the agent checks the inbox automatically and acts on or escalates new emails. Free tier includes 1 address and 1000 requests/day.
---

# BotEmail.ai - Email for Bots

Professional email infrastructure for autonomous agents. Create permanent bot email addresses and manage inboxes programmatically.

## Setup — Proactive Inbox Monitoring

When the user wants the agent to **monitor their BotEmail inbox automatically**, do the following:

### Step 1: Get credentials

Ask the user for (or look up from TOOLS.md):
- Their BotEmail address (e.g. `mybot_bot@botemail.ai`)
- Their API key

If they don't have an account yet, create one:

```
POST https://api.botemail.ai/api/create-account
Content-Type: application/json

{}
```

### Step 2: Save credentials to TOOLS.md

**Security note:** `TOOLS.md` is a local workspace file — it lives only on this machine, is never sent to any server, and should never be committed to a public repo. It is the standard OpenClaw location for local service credentials (equivalent to a `.env` file).

Append to the workspace `TOOLS.md`:

```markdown
### BotEmail.ai
- **Address:** `mybot_bot@botemail.ai`
- **API Key:** `their-api-key`
- **Inbox API:** `GET https://api.botemail.ai/api/emails/mybot_bot@botemail.ai`
- **Auth:** `Authorization: Bearer their-api-key`
- **Delete email:** `DELETE https://api.botemail.ai/api/emails/mybot_bot@botemail.ai/{emailId}`
```

### Step 3: Update HEARTBEAT.md

**Ask the user first:** Before enabling monitoring, confirm: *"Should I check your inbox on every heartbeat and notify you of new emails? I can also act autonomously on safe requests (lookups, reminders) if you'd like — just say the word."* Only add monitoring if they confirm.

Add (or replace) the email section in the workspace `HEARTBEAT.md`:

```markdown
## 📬 Email Inbox Check — mybot_bot@botemail.ai

On every heartbeat, check the bot's inbox and notify the user of new emails.

### Steps

1. Fetch inbox:
   ```
   GET https://api.botemail.ai/api/emails/mybot_bot@botemail.ai
   Authorization: Bearer their-api-key
   ```

2. Load seen email IDs from `memory/heartbeat-state.json` (key: `seenEmailIds`, default: `[]`)

3. For each email NOT in `seenEmailIds`:
   - Read subject + body
   - **Always notify the user** with sender, subject, and a brief summary
   - Only act autonomously (web search, reminders, lookups) if the user has explicitly enabled it
   - **Never act autonomously** on: sending emails, deletions, public posts, or anything touching private data
   - Add the email ID to `seenEmailIds` after processing

4. Save updated `seenEmailIds` back to `memory/heartbeat-state.json`

### Notification format
> 📬 **New email** from [sender]
> **Subject:** [subject]
> [1-2 sentence summary]

If inbox is empty or all emails already seen → HEARTBEAT_OK
```

### Step 4: Initialise state file

Create `memory/heartbeat-state.json` if it doesn't exist:
```json
{"seenEmailIds": [], "lastChecks": {}}
```

### Done!
Tell the user their inbox is now being monitored. They'll be notified on every heartbeat (~30 min) when new emails arrive.

---

## Manual Inbox Operations

### Check Inbox

```
GET https://api.botemail.ai/api/emails/{email}
Authorization: Bearer YOUR_API_KEY
```

Response:
```json
{
  "emails": [
    {
      "id": "abc123",
      "from": "sender@example.com",
      "subject": "Hello",
      "timestamp": "2026-02-17T12:00:00Z",
      "bodyText": "Hello from BotEmail!"
    }
  ]
}
```

### Get Single Email

```
GET https://api.botemail.ai/api/emails/{email}/{id}
Authorization: Bearer YOUR_API_KEY
```

### Delete Email

```
DELETE https://api.botemail.ai/api/emails/{email}/{id}
Authorization: Bearer YOUR_API_KEY
```

### Clear Inbox

```
DELETE https://api.botemail.ai/api/emails/{email}
Authorization: Bearer YOUR_API_KEY
```

---

## Quick Start (New Account)

```bash
curl -X POST https://api.botemail.ai/api/create-account \
  -H "Content-Type: application/json" \
  -d '{}'
```

Returns: `{ "email": "9423924_bot@botemail.ai", "apiKey": "..." }`

Custom username:
```bash
curl -X POST https://api.botemail.ai/api/create-account \
  -H "Content-Type: application/json" \
  -d '{"username": "mybot"}'
```

---

## Rate Limits

- **Free Tier**: 1 bot address, 1000 requests/day

## Links

- **Dashboard**: https://botemail.ai/dashboard
- **Docs**: https://botemail.ai/docs
- **MCP Server**: https://github.com/claw-silhouette/botemail-mcp-server
