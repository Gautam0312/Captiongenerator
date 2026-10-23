# Client-Side Caption Generator — v1 (Stable Base)

A fully client-side (browser-only) tool for generating precise, editable captions
from a video/audio file using your **exact narration script** — no server,
no API keys, no data leaving the browser.

---

## What it does

1. **Transcribes audio** in-browser using Whisper-tiny (via Transformers.js),
   producing word-level timestamps.
2. **Aligns your exact script** (pasted as plain text, one caption per line)
   to those timestamps using a Needleman-Wunsch sequence alignment algorithm.
   This means the **output text is always your exact wording** — Whisper is
   only used to infer *timing*, not to generate the final text.
3. Produces an **editable caption list** with a live video preview and
   caption overlay, plus SRT/VTT export.

---

## Core Architecture

| Stage | Technology | Notes |
|---|---|---|
| Audio extraction | Web Audio API (`decodeAudioData`, `OfflineAudioContext`) | Downmixes to mono, resamples to 16kHz for Whisper |
| Speech-to-timestamp | `@xenova/transformers` (Whisper-tiny, ONNX) | Tries WebGPU first, falls back to WASM automatically |
| Script-to-audio alignment | Custom Needleman-Wunsch global alignment (vanilla JS) | Matches normalized script words to normalized Whisper words, carries over timestamps |
| Caption UI | Vanilla JS + HTML/CSS, two-column layout | No frameworks, no build step |
| Export | Custom SRT/VTT string builders | Standard subtitle formats |

Everything runs **100% client-side** — the video/audio file is never uploaded anywhere.

---

## Features (as of this version)

- ✅ Upload video/audio, see live preview
- ✅ One-click Whisper transcription (word-level timestamps)
- ✅ Paste exact script → aligns script text to real audio timing
- ✅ Two-column layout: video + script (left), caption editor (right, sticky scroll)
- ✅ Configurable character limit, with live highlighting of over-limit lines in the script editor
- ✅ Per-caption character count warnings
- ✅ Editable start/end time, text, per caption line
- ✅ **Trim**: "Set Start/End = Playhead" buttons to snap boundaries to current video position
- ✅ **Merge**: combine a caption with the next one (text + time range)
- ✅ **Split at Cursor**: click inside caption text to set cursor position, then split exactly there (splits both text and time proportionally)
- ✅ **Overlap detection**: captions with overlapping time ranges are highlighted in red with a warning
- ✅ **No-gap timing**: each caption's start time = previous caption's end time (no silent gaps); first caption starts at the actual detected speech onset
- ✅ Add new blank caption lines manually
- ✅ Delete caption lines
- ✅ Live caption overlay on video during playback
- ✅ Export to **SRT** and **VTT**
- ✅ `beforeunload` popup neutralized (prevents accidental "leave site?" prompts)

---

## How to run

1. Save the HTML file locally.
2. Serve it with a simple local server (required for ES module imports to work reliably):
   ```bash
   npx serve .
   # or
   python3 -m http.server
