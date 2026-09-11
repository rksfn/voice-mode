# Voice Mode

Context-aware voice input for [pi](https://pi.dev). Speak a coding request; a formulated prompt is inserted at the cursor for review. It never auto-submits.

```text
microphone
  → OpenRouter Whisper Large V3 Turbo
  → cwd, loaded context files, recent conversation, editor draft
  → GPT-5.6 Luna
  → formulated prompt inserted at the cursor
```

Recording currently requires macOS and `ffmpeg`. Transcription uses OpenRouter. The rewrite step uses OpenAI Codex when configured, otherwise OpenRouter.

## Install

```bash
pi install git:github.com/rksfn/voice-mode
```

Without installing, from this repo:

```bash
pi -e ./extensions/voice-mode/index.ts
```

## Use

Inside pi:

```text
/voice tap     Ctrl+Space starts; Ctrl+Space stops
/voice hold    hold Ctrl+Space to record; release to stop
/voice off
/voice status
```

`hold` needs a terminal with Kitty keyboard protocol support so pi receives key-release events. `tap` works as the fallback.

The extension:

1. Records with `ffmpeg`.
2. Transcribes with OpenRouter Whisper (pi's OpenRouter login or `OPENROUTER_API_KEY`).
3. Gives the transcript, cwd, loaded context files, recent conversation, and current editor draft to GPT-5.6 Luna.
4. Inserts the formulated prompt at the cursor.

## Configuration

```bash
VOICE_MODE_AUDIO_DEVICE=":0"               # ffmpeg avfoundation input
VOICE_MODE_STT_MODEL=openai/whisper-large-v3-turbo
VOICE_MODE_LANGUAGE=en
```
