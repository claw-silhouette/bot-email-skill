---
name: bot-email
description: Get free bot email addresses from BotEmail.ai. Create instant inboxes, receive emails, extract verification codes, monitor for messages. Use when an agent needs an email address for signup flows, 2FA codes, or email automation. No human setup required.
license: MIT
metadata:
  author: devgmstudios
  version: "1.0.1"
---

# BotEmail.ai — Free Bot Email Inboxes

Give your agent its own email address instantly. No human signup. No OAuth. Just an API.

**Base URL:** `https://api.botemail.ai`

## Create an inbox

```bash
curl -X POST https://api.botemail.ai/api/create-account \
  -H "Content-Type: application/json" \
  -d '{}'
```

Returns: `{"email":"abc123_bot@botemail.ai","apiKey":"sk-..."}`

## Read emails

```bash
curl https://api.botemail.ai/api/emails/YOUR_EMAIL \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Extract verification codes

Check `subject` and `body` of emails for patterns like 6-digit codes, links with tokens, etc.

## Delete email

```bash
curl -X DELETE https://api.botemail.ai/api/emails/YOUR_EMAIL/EMAIL_ID \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Use cases

- Bot service registration and signup flows
- Email verification code extraction
- Account confirmation automation
- 2FA code monitoring
- Notification monitoring

## Environment variables

Set `BOTEMAIL_API_KEY` and `BOTEMAIL_ADDRESS` in your agent environment, or store in OpenClaw config.

👉 [botemail.ai](https://botemail.ai) | [Dashboard](https://botemail.ai/dashboard)