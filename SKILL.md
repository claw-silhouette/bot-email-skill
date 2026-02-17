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

### Step 2: Save to TOOLS.md

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

Add (or replace) the email section in the workspace `HEARTBEAT.md`:

```markdown
## 📬 Email Inbox Check — mybot_bot@botemail.ai

On every heartbeat, check the bot's inbox for new emails and act proactively.

### Steps

1. Fetch inbox:
   ```
   GET https://api.botemail.ai/api/emails/mybot_bot@botemail.ai
   Authorization: Bearer their-api-key
   ```
   Use `web_fetch` with the URL above.

2. Load seen email IDs from `memory/heartbeat-state.json` (key: `seenEmailIds`, default: `[]`)

3. For each email NOT in `seenEmailIds`:
   - Read subject + body
   - **Decide autonomously** if the request is clear and safe (e.g. "search for X", "remind me of Y", "what's the weather") → do it and reply to the user with the result
   - **Escalate to user** if ambiguous, sensitive, or needs approval → send a message saying who emailed and what they want
   - Always add the email ID to `seenEmailIds` after processing

4. Save updated `seenEmailIds` back to `memory/heartbeat-state.json`

### What counts as "act autonomously"
- Informational lookups (web search, weather, definitions)
- Setting reminders (use cron tool)
- Summarising content from a URL
- Answering factual questions

### What to escalate
- Requests to send emails, post publicly, or delete things
- Anything requiring judgment about the user's private data
- Requests you're not confident about

### Notification format
> 📬 **New email** from [sender]
> **Subject:** [subject]
> [1-2 sentence summary or result if you acted on it]

If inbox is empty or all emails already seen → HEARTBEAT_OK
```

### Step 4: Initialise state file

Create `memory/heartbeat-state.json` if it doesn't exist:
```json
{"seenEmailIds": [], "lastChecks": {}}
```

### Done!
Tell the user their inbox is now being monitored. Every heartbeat (~30 min), new emails will be processed automatically.

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
