# Telegram Send Attachment Skill

A comprehensive guide for AI agents to correctly send file attachments via Telegram Bot API.

## Overview

This skill provides clear instructions and examples for sending file attachments through Telegram's Bot API. It was specifically designed to help AI agents like OpenClaw, Hermes, and similar systems correctly send files as attachments in Telegram chats.

## Motivation

AI agents often struggle when attempting to send file attachments via Telegram. A common issue is that instead of sending the actual file as an attachment, agents mistakenly send the file's content as plain text in the chat. This creates a poor user experience and defeats the purpose of file sharing.

This skill was created to:
- **Provide clear, unambiguous instructions** on how to send files as proper attachments
- **Prevent common mistakes** such as reading file content and sending it as text
- **Offer ready-to-use curl examples** that agents can directly execute
- **Document limits and restrictions** to avoid API errors

## What This Skill Covers

- ✅ Sending documents (any file type: PDF, TXT, DOC, XLS, ZIP, etc.)
- ✅ Sending images (with inline preview)
- ✅ Sending videos (with inline player)
- ✅ Sending audio files (with inline player)
- ✅ File size limits and format restrictions
- ✅ Common error handling and troubleshooting

### Why curl?

This skill uses `curl` exclusively because:
- **Universal** - Available on virtually all systems
- **No Dependencies** - No need to install Python libraries or other packages
- **Direct API Calls** - Makes direct HTTP requests to Telegram API
- **Agent-Friendly** - Easy for AI agents to execute without setup
- **Reliable** - No version conflicts or dependency issues

## Installation

### For AI Agent Systems

1. **Copy the skill file** to your agent's skills directory:
   ```bash
   cp SKILL.md /path/to/your/agent/skills/telegram-send-attachment/
   ```

2. **Register the skill** in your agent's configuration (if required by your system)

3. **Ensure curl is available** in your agent's execution environment:
   ```bash
   curl --version
   ```

### For Manual Use

Simply reference the `SKILL.md` file when you need to send attachments via Telegram Bot API.

## Prerequisites

Before using this skill, you need:

1. **Telegram Bot Token** - Obtain from [@BotFather](https://t.me/botfather) on Telegram
2. **Chat ID** - The target chat/channel ID where files will be sent
3. **curl** - Command-line tool for making HTTP requests (pre-installed on most systems)

## Quick Start

### 1. Get Your Bot Token
```bash
# Start a chat with @BotFather on Telegram
# Send: /newbot
# Follow the instructions to create your bot
# Copy the bot token provided
```

### 2. Get Your Chat ID
```bash
# Send a message to your bot on Telegram
# Then run:
curl https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates

# Look for "chat":{"id": YOUR_CHAT_ID}
```

### 3. Send Your First Attachment
```bash
curl -X POST \
  -F chat_id="<YOUR_CHAT_ID>" \
  -F document="@/path/to/file.pdf" \
  -F caption="Document sent" \
  "https://api.telegram.org/bot<YOUR_TOKEN>/sendDocument"
```

## Usage Examples

### Send a Document
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F document="@/path/to/document.pdf" \
  -F caption="Important document" \
  "https://api.telegram.org/bot<TOKEN>/sendDocument"
```

### Send an Image
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F photo="@/path/to/photo.jpg" \
  -F caption="Check this out!" \
  "https://api.telegram.org/bot<TOKEN>/sendPhoto"
```

### Send a Video
```bash
curl -X POST \
  -F chat_id="<CHAT_ID>" \
  -F video="@/path/to/video.mp4" \
  -F caption="Watch this" \
  "https://api.telegram.org/bot<TOKEN>/sendVideo"
```

## Key Points for AI Agents

### ✅ DO:
- Use `@/path/to/file` syntax for file paths in curl commands
- Send files as multipart/form-data using `-F` flag
- Use the appropriate method for each file type (`sendDocument`, `sendPhoto`, `sendVideo`, `sendAudio`)
- Check file size limits before sending
- Execute curl commands directly - do NOT read file content first

### ❌ DON'T:
- **NEVER read file content and send it as text** - This is the most common mistake
- Send file paths as strings without the `@` prefix
- Exceed file size limits (50 MB for documents, 10 MB for photos)
- Try to use `sendDocument` for files larger than 50 MB
- Replace curl with other methods unless absolutely necessary

### Critical: File vs Content

**WRONG** ❌
```bash
# Reading file content and sending as text
content=$(cat /path/to/file.pdf)
curl -X POST -d "text=$content" ...
```

**CORRECT** ✅
```bash
# Sending file as attachment
curl -X POST -F document="@/path/to/file.pdf" ...
```

The `@` symbol in curl tells it to send the **file itself**, not its content.

## File Size Limits

| Type | Max Size | Format |
|------|----------|--------|
| Documents | 50 MB | Any file type |
| Photos | 10 MB | JPG, PNG, GIF |
| Videos | 50 MB | MP4 (recommended) |
| Audio | 50 MB | MP3, OGG |

## Troubleshooting

| Error | Solution |
|-------|----------|
| 400 Bad Request | Verify the chat_id is correct |
| 403 Forbidden | Bot is not in the group/channel |
| 404 Not Found | Bot token is invalid |
| Upload fails | Check file size and format |

## Contributing

Contributions are welcome! If you find issues or have suggestions for improvements:

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

This skill is provided as-is for educational and practical purposes.

## References

- [Telegram Bot API](https://core.telegram.org/bots/api)
- [sendDocument Method](https://core.telegram.org/bots/api#sendDocument)
- [sendPhoto Method](https://core.telegram.org/bots/api#sendPhoto)
- [sendVideo Method](https://core.telegram.org/bots/api#sendVideo)
- [sendAudio Method](https://core.telegram.org/bots/api#sendAudio)

## Version

Current version: 1.2.0

**Changelog:**
- v1.2.0: Removed Python examples, focused on curl-only approach, added critical warnings about file vs content
- v1.1.0: Initial release with curl and Python examples

## Author

Leonardo Dias

---

**Note:** This skill is specifically designed to help AI agents avoid common mistakes when sending file attachments via Telegram. Always ensure you're sending the actual file as an attachment, not its content as text.
