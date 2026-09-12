# Voice Mode

Context-aware voice input for [pi](https://pi.dev). Speak a coding request; a formulated prompt is inserted at the cursor for review. It never auto-submits.

```text
microphone
  → OpenRouter Whisper Large V3 Turbo, segment by segment while you speak
  → cwd, loaded context files, recent conversation, editor draft
  → GPT-5.6 Luna
  → formulated prompt inserted at the cursor
```

Recording currently requires macOS and `ffmpeg`. Transcription uses OpenRouter. The rewrite step uses OpenAI Codex when configured, otherwise OpenRouter.

## Install

```bash
pi install npm:@rksfn/voice-mode
```

From git:

```bash
pi install git:github.com/rksfn/voice-mode
```

Without installing, from this repo:

```bash
pi -e ./extensions/voice-mode/index.ts
```

## Use

Voice input starts in `tap` mode:

```text
Ctrl+Space     start recording
Ctrl+Space     stop recording
```

Inside pi, use `/voice` to change modes:

```text
/voice tap     Ctrl+Space starts; Ctrl+Space stops
/voice hold    hold Ctrl+Space to record; release to stop
/voice off
/voice status
```

`hold` needs a terminal with Kitty keyboard protocol support so pi receives key-release events. `tap` is the default and works as the fallback.

The extension:

1. Records with `ffmpeg`.
2. Transcribes with OpenRouter Whisper (pi's OpenRouter login or `OPENROUTER_API_KEY`) while you are still speaking: audio is cut into segments at pauses, and each segment is sent as soon as it is cut, so only the last segment is outstanding when you stop.
3. Gives the transcript, cwd, loaded context files, recent conversation, and current editor draft to GPT-5.6 Luna.
4. Inserts the formulated prompt at the cursor.

## Configuration

```bash
VOICE_MODE_AUDIO_DEVICE=":0"               # ffmpeg avfoundation input
VOICE_MODE_STT_MODEL=openai/whisper-large-v3-turbo
VOICE_MODE_LANGUAGE=en
```
