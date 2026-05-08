# groq-whisper-transcription

A skill that enables AI agents like **OpenClaw** and **Hermes** to understand and respond to audio/voice messages naturally — as if they had received a plain text message.

## How it works

When an audio or voice message arrives (from Telegram or WhatsApp), the agent uses the [Groq](https://groq.com) API with Whisper models to transcribe the audio. The transcribed text is then treated as the user's message, and the agent responds to its content directly — with no mention of audio, transcription, or the original voice message.

From the user's perspective, the interaction feels seamless: they send a voice message and receive a natural, contextual reply.

## Key features

- Supports Telegram and WhatsApp audio formats (`.ogg`, `.oga`, `.opus`, `.m4a`, `.mp3`, `.wav`)
- Uses `curl` only — no Python SDK or extra dependencies required
- Returns plain text via `response_format=text`, no JSON parsing needed
- Optional `ffmpeg` conversion for unsupported formats
- Agent never exposes the transcription or references the audio in its response

## Requirements

| Requirement | Details |
|-------------|---------|
| `GROQ_API_KEY` | Environment variable with a valid Groq API key |
| `curl` | Available on PATH |
| `ffmpeg` | Optional, needed only for format conversion |

## Quick example

```bash
curl -s \
  -H "Authorization: Bearer $GROQ_API_KEY" \
  -F "file=@/path/to/audio.ogg" \
  -F "model=whisper-large-v3-turbo" \
  -F "language=pt" \
  -F "response_format=text" \
  https://api.groq.com/openai/v1/audio/transcriptions
```

## Supported platforms

| Platform  | Typical format                  |
|-----------|---------------------------------|
| Telegram  | `.ogg` / `.oga` (Opus)          |
| WhatsApp  | `.ogg` (Opus) or `.m4a` (AAC)   |

## Audio conversion (when needed)

```bash
# For .oga / .opus (Telegram)
ffmpeg -y -i input.oga -ar 16000 -ac 1 output.wav

# For .m4a (WhatsApp)
ffmpeg -y -i input.m4a -ar 16000 -ac 1 output.wav
```

## Files

- [`SKILL.md`](./SKILL.md) — Full skill instructions for AI agents

## License

MIT — Leonardo Dias
