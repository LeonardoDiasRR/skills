---
name: groq-whisper-transcription
description: Transcribes audio/voice messages from Telegram and WhatsApp using Groq Whisper via OpenAI-compatible endpoint.
version: 1.0.0
author: Leonardo Dias / Hermes
license: MIT
tags:
  - telegram
  - whatsapp
  - audio
  - voice
  - transcription
  - groq
  - whisper
  - curl
required_environment_variables:
  - GROQ_API_KEY
---

# Groq Whisper Transcription

Use this skill when you need to transcribe audio/voice messages received on Telegram or WhatsApp using the Groq API with Whisper models.

## Goal

Convert audio files (`.ogg`, `.oga`, `.opus`, `.mp3`, `.wav`, `.m4a`, etc.) to text, from voice messages originating from Telegram or WhatsApp.

## Prerequisites

- `GROQ_API_KEY` environment variable set.
- `curl` available on PATH.
- `ffmpeg` installed, if audio conversion is needed before sending.

## Recommended models

- `whisper-large-v3-turbo`: faster and cheaper for most use cases.
- `whisper-large-v3`: higher quality, when speed/cost are less of a concern.

## General flow

1. Identify the received voice/audio file (Telegram or WhatsApp).
2. Download or locate the file at the local path provided by the gateway.
3. If the format is not supported, convert with `ffmpeg` (see section below).
4. Send the file to Groq at `https://api.groq.com/openai/v1/audio/transcriptions`.
5. Use the transcribed text as the user's message and respond to its content naturally, as if a text message had been received.

> **Important:** Never expose the transcription to the user. Do not mention that audio was received, that a transcription was performed, or reference the original audio in any way. Simply respond to the content of the message.

### Formats by platform

| Platform  | Typical format              | Conversion needed?        |
|-----------|-----------------------------|---------------------------|
| Telegram  | `.ogg` / `.oga` (Opus)      | Usually not               |
| WhatsApp  | `.ogg` (Opus) or `.m4a` (AAC) | Sometimes (`m4a` → `wav`) |

## curl command

Use `response_format=text` to receive plain text directly, without needing to parse JSON:

```bash
curl -s \
  -H "Authorization: Bearer $GROQ_API_KEY" \
  -F "file=@/path/to/audio.ogg" \
  -F "model=whisper-large-v3-turbo" \
  -F "language=pt" \
  -F "response_format=text" \
  https://api.groq.com/openai/v1/audio/transcriptions
```

The output is the transcribed text directly, with no JSON wrapper.

## Audio conversion with ffmpeg

If the file comes in an unsupported format, convert it to `.wav`:

```bash
ffmpeg -y -i /path/to/input.oga -ar 16000 -ac 1 /tmp/audio_to_transcribe.wav
```

For WhatsApp `.m4a` files:

```bash
ffmpeg -y -i /path/to/input.m4a -ar 16000 -ac 1 /tmp/audio_to_transcribe.wav
```

Then transcribe `/tmp/audio_to_transcribe.wav`.

## Best practices

- Never send binary content as text: always use file upload (`@/path/to/audio` in `curl`).
- Use `response_format=text` to get plain text without parsing JSON.
- Set `language=pt` for Portuguese messages; this reduces errors and speeds up transcription.
- Treat the transcribed text as the user's message — respond to its meaning, never to the fact that it was an audio.
- Never mention transcription, voice message, or audio in the response.
- Preserve the original intent of the transcribed message when formulating the response.
- For long audio files, split into smaller parts before transcribing.

## Common error handling

### Missing `GROQ_API_KEY`

Check if the variable exists:

```bash
printenv GROQ_API_KEY
```

### Rejected audio format

Convert with ffmpeg:

```bash
ffmpeg -y -i input.ogg -ar 16000 -ac 1 output.wav
```

### Empty or poor response

- Try `whisper-large-v3` instead of `whisper-large-v3-turbo`.
- Set `language=pt`.
- Improve audio quality by removing noise or increasing volume.
- Confirm the file exists and is readable before sending.

## Response behavior

After transcription, respond directly to the content of the message as if it were a plain text message. Examples:

- User sends audio: *"What time is our meeting tomorrow?"*
  → Respond: *"Our meeting is at 10 AM."*
  → **Never:** *"Transcription: What time is our meeting tomorrow?"*

- User sends audio: *"Can you help me write an email?"*
  → Respond naturally to the request.
  → **Never** reference the audio, the transcription, or the fact that a voice message was received.
