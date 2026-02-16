---
name: bot-email
description: Professional email infrastructure for autonomous agents. Create and manage bot email accounts on BotEmail.ai for testing, automation, and email workflows. Free tier includes 1 address and 1000 requests/day.
---

# BotEmail.ai - Email for Bots

Professional email infrastructure for autonomous agents. Create permanent bot email addresses and manage inboxes programmatically.

## Quick Start

### Create Random Bot Email

```bash
curl -X POST https://api.botemail.ai/api/create-account \
  -H "Content-Type: application/json" \
  -d '{}'
```

Returns: `9423924_bot@botemail.ai` (random 7-digit number)

### Create Custom Bot Email

```bash
curl -X POST https://api.botemail.ai/api/create-account \
  -H "Content-Type: application/json" \
  -d '{"username": "mybot"}'
```

Returns: `mybot_bot@botemail.ai`

### Check Inbox

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  https://api.botemail.ai/api/emails/mybot_bot@botemail.ai
```

## API Base

```
https://api.botemail.ai
```

## Authentication

Include API key in request header:
- `Authorization: Bearer YOUR_API_KEY` (preferred)
- `X-API-Key: YOUR_API_KEY` (alternative)

No authentication required for account creation.

## Endpoints

### POST /api/create-account

Create a new bot email account.

**Body (optional):**
```json
{
  "username": "mybot"  // Optional: omit for random
}
```

**Response:**
```json
{
  "email": "mybot_bot@botemail.ai",
  "apiKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "message": "Account created!"
}
```

### GET /api/emails/{email}

Get all emails in inbox.

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

**Response:**
```json
{
  "emails": [
    {
      "id": "abc123",
      "from": "sender@example.com",
      "to": "mybot_bot@botemail.ai",
      "subject": "Welcome!",
      "timestamp": "2026-02-16T12:00:00Z",
      "bodyText": "Hello from BotEmail!"
    }
  ]
}
```

### GET /api/emails/{email}/{id}

Get specific email by ID.

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

**Response:**
```json
{
  "id": "abc123",
  "from": "sender@example.com",
  "to": "mybot_bot@botemail.ai",
  "cc": null,
  "subject": "Welcome!",
  "timestamp": "2026-02-16T12:00:00Z",
  "bodyText": "Hello from BotEmail!",
  "bodyHtml": "<p>Hello from BotEmail!</p>"
}
```

### DELETE /api/emails/{email}/{id}

Delete a specific email.

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

### DELETE /api/emails/{email}

Clear entire inbox.

**Headers:**
```
Authorization: Bearer YOUR_API_KEY
```

## Common Patterns

### Bot Registration Flow

1. Create bot email account
2. Use email for service registration
3. Poll inbox for verification email
4. Extract verification code/link
5. Complete registration

### Email Verification Monitor

```javascript
async function waitForVerification(email, apiKey, timeout = 300000) {
  const startTime = Date.now();
  
  while (Date.now() - startTime < timeout) {
    const response = await fetch(
      `https://api.botemail.ai/api/emails/${email}`,
      { headers: { 'Authorization': `Bearer ${apiKey}` } }
    );
    
    const data = await response.json();
    
    if (data.emails && data.emails.length > 0) {
      const verificationEmail = data.emails.find(e => 
        e.subject.toLowerCase().includes('verify') ||
        e.subject.toLowerCase().includes('confirm')
      );
      
      if (verificationEmail) {
        return verificationEmail;
      }
    }
    
    await new Promise(resolve => setTimeout(resolve, 10000));
  }
  
  throw new Error('Verification email timeout');
}
```

### Extract Verification Code

```javascript
function extractCode(emailBody) {
  const patterns = [
    /code[:\s]+([A-Z0-9]{6,8})/i,
    /verification code[:\s]+([A-Z0-9]{6,8})/i,
    /your code is[:\s]+([A-Z0-9]{6,8})/i,
    /\b([A-Z0-9]{6})\b/,
  ];
  
  for (const pattern of patterns) {
    const match = emailBody.match(pattern);
    if (match) return match[1];
  }
  
  return null;
}
```

## Use Cases

- 🤖 **Bot service registration** - Automated signup flows
- 📧 **Email verification** - Extract verification codes
- 🔐 **2FA monitoring** - Retrieve authentication codes
- 🔔 **Notification monitoring** - Watch for specific emails
- 📊 **Testing workflows** - End-to-end email testing
- 🤝 **Multi-bot management** - Separate inbox per bot

## Rate Limits

- **Free Tier (Only)**: 1 bot address, 1000 requests/day

Contact support@botemail.ai for higher limits or enterprise plans.

## Error Codes

- `400` - Validation error (username taken, invalid format)
- `401` - Authentication failed (invalid API key)
- `404` - Account or email not found
- `429` - Rate limit exceeded
- `500` - Server error

## Features

- ✅ **Permanent inboxes** - Email addresses never expire
- ✅ **Receive-only** - No sending capability (security by design)
- ✅ **Auto-expire** - Individual emails expire after 6 months
- ✅ **JSON-first** - Bot-friendly API responses
- ✅ **Private** - Each bot has isolated inbox
- ✅ **Webhook support** - Push notifications for new emails
- ✅ **Web dashboard** - Human-accessible inbox viewer
- ✅ **Random usernames** - Instant throwaway addresses

## Web Dashboard

Human-accessible inbox viewer at: https://botemail.ai/dashboard

Enter bot email + API key to view inbox in browser.

## Documentation

- **Full API Docs**: https://botemail.ai/docs
- **Main Site**: https://botemail.ai
- **MCP Server**: https://github.com/claw-silhouette/botemail-mcp-server

## MCP Server

For Claude Desktop integration, install the MCP server:

```bash
git clone https://github.com/claw-silhouette/botemail-mcp-server.git
cd botemail-mcp-server
npm install
```

See MCP server repo for full configuration instructions.

## Support

- **Email**: support@botemail.ai
- **Issues**: Report via email or GitHub issues

## License

Free to use. See https://botemail.ai for terms.
