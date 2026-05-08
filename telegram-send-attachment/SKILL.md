---
name: telegram-send-attachment
description: Send file attachments (PDF, images, documents, videos) via Telegram Bot API as attached messages
category: social-media
version: 1.2.0
author: Leonardo Dias
---

# Sending File Attachments via Telegram

## Objective
Send files as attachments (documents, images, videos, audio) to Telegram chats using the Telegram Bot API.

## Setup

### 1. Required Credentials
To send messages and attachments, you will need:
- A **Bot Token** (obtained from @BotFather on Telegram)
- A **Chat ID** (target chat/channel ID where the file will be sent)

---

## Usage

This skill uses `curl` for sending attachments because:
- ✅ **Universal** - Works on any system without additional libraries
- ✅ **Direct** - Makes direct HTTP calls to Telegram API
- ✅ **Reliable** - No dependency management or version conflicts
- ✅ **Simple** - Easy for AI agents to execute and understand

## Important: Sending Files vs Content

**CRITICAL:** Always send the file itself, not its content!

**WRONG** ❌
```bash
# Reading and sending file content as text
content=$(cat /path/to/file.pdf)
curl -d "text=$content" ...
```

**CORRECT** ✅
```bash
# Sending file as attachment (note the @ symbol)
curl -F document="@/path/to/file.pdf" ...
```

The `@` symbol in curl indicates the value is a **file path**, and curl will upload the file.

---

## Examples

### Send a Document (any file type)
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F document="@/path/to/<FILENAME>" \
  -F caption="Document sent" \
  -F parse_mode="Markdown" \
  "https://api.telegram.org/bot<TOKEN>/sendDocument"
```

### Send an Image
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F photo="@/path/to/image.png" \
  -F caption="Photo sent" \
  "https://api.telegram.org/bot<TOKEN>/sendPhoto"
```

### Send a Video
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F video="@/path/to/video.mp4" \
  -F caption="Video sent" \
  "https://api.telegram.org/bot<TOKEN>/sendVideo"
```

### Send Audio (voice note)
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F audio="@/path/to/audio.ogg" \
  -F caption="Audio sent" \
  "https://api.telegram.org/bot<TOKEN>/sendAudio"
```

---

## Limits and Restrictions

| Type | Max Size | Format |
|------|----------|--------|
| Documents | 50 MB | Any file type |
| Photos | 10 MB | JPG, PNG, GIF |
| Videos | 50 MB | MP4 (recommended) |
| Audio | 50 MB | MP3, OGG |
| Videos (small) | 50 MB | GIF (non-looping) |

**Note:** The `sendDocument` method accepts any file type (PDF, TXT, DOC, XLS, ZIP, etc.) up to 50 MB.

## Pitfalls & Solutions

| Issue | Solution |
|------|------|
| 400 Bad Request | Verify the chat_id is correct |
| 403 Forbidden | Bot is not in the group/channel |
| 404 Not Found | Bot token is invalid |
| Upload fails | Check maximum file size |
| Timeout on response | Use sending via `FILE_ID` from Telegram server |

## Bot Status Verification

```bash
# Bot status
curl https://api.telegram.org/bot<TOKEN>/getMe

# Latest updates
curl https://api.telegram.org/bot<TOKEN>/getUpdates
```

## References
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [sendDocument Method](https://core.telegram.org/bots/api#sendDocument)
- [sendPhoto Method](https://core.telegram.org/bots/api#sendPhoto)
- [sendVideo Method](https://core.telegram.org/bots/api#sendVideo)
- [sendAudio Method](https://core.telegram.org/bots/api#sendAudio)
