# Voice Mode

Context-aware voice input for [pi](https://pi.dev). Speak a coding request; a formulated prompt is inserted at the cursor. By default it waits there for review; `/voice send auto` submits it for you.

```text
microphone
  → OpenRouter Whisper Large V3 Turbo, segment by segment while you speak
  → cwd, loaded context files, recent conversation, editor draft
  → GPT-5.6 Luna
  → formulated prompt inserted at the cursor, held or sent
```

Recording requires `ffmpeg` on macOS, Linux, or Windows. Transcription uses OpenRouter. The rewrite step uses OpenAI Codex when configured, otherwise OpenRouter. If Codex fails, formulation falls back to OpenRouter.

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
/voice tap           Ctrl+Space starts; Ctrl+Space stops
/voice hold          hold Ctrl+Space to record; release to stop
/voice off
/voice send auto     submit the formulated prompt automatically
/voice send manual   leave it in the editor for review (default)
/voice status
```

`hold` needs a terminal with Kitty keyboard protocol support so pi receives key-release events. `tap` is the default and works as the fallback.

`send` is remembered across sessions in `~/.pi/agent/voice-mode.json`; the recording mode is not and starts at `tap` each run. With `send auto`, anything already in the editor is submitted along with the formulated prompt, exactly as if you had pressed enter, so a voice note during a running turn steers that turn.

The extension:

1. Records with `ffmpeg`.
2. Transcribes with OpenRouter Whisper (pi's OpenRouter login or `OPENROUTER_API_KEY`) while you are still speaking: audio is cut into segments at pauses, and each segment is sent as soon as it is cut, so only the last segment is outstanding when you stop.
3. Gives the transcript, cwd, loaded context files, recent conversation, and current editor draft to GPT-5.6 Luna.
4. Inserts the formulated prompt at the cursor, and submits it when `send` is `auto`.

## Configuration

```bash
VOICE_MODE_AUDIO_DEVICE=":0"               # ffmpeg input device (see below)
VOICE_MODE_STT_MODEL=openai/whisper-large-v3-turbo
VOICE_MODE_LANGUAGE=en
```

Recording picks the OS's default microphone unless `VOICE_MODE_AUDIO_DEVICE` is set:

- **macOS**: `ffmpeg -f avfoundation`. Default is `:0` (first listed device). List devices with `ffmpeg -f avfoundation -list_devices true -i ""`.
- **Linux**: `ffmpeg -f pulse`, which also works on PipeWire via `pipewire-pulse`. Default is `default`. List sources with `pactl list short sources`.
- **Windows**: `ffmpeg -f dshow`, which has no built-in "default" device, so voice-mode enumerates devices and uses the first audio capture device found. List devices with `ffmpeg -f dshow -list_devices true -i dummy` and set `VOICE_MODE_AUDIO_DEVICE` to the exact device name to pick a specific one.
