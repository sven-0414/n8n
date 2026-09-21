# n8n Transcription Pipeline

This project turns audio and video files into text transcripts using a local n8n workflow. Everything runs on my own machine.

## Where the transcripts are

Finished transcripts are saved in the `out/` folder. 

- **`filename.srt`** — subtitles for the video, with timestamps. Can be loaded into a video player (VLC, browser, etc.) to show captions.
- **`filename.txt`** — the plain raw text from whisper.cpp, without timestamps.
- **`filename.cleaned.txt`** — the same text after it has been cleaned up by the AI (Qwen 2.5 14B via Ollama): punctuation added, obvious errors fixed, and formatted for easier reading.

If you just want to read the transcript, use the `.cleaned.txt` file. If you want to follow along with the video, use the `.srt` file.

## How the transcripts are made

1. **n8n** runs in a Docker container and watches a `inbox/` folder for new files.
2. When a new file arrives, n8n calls a small **FastAPI** service.
3. The FastAPI service uses **ffmpeg** to pull the audio out of the file and convert it to a format Whisper can read.
4. The audio is sent to **whisper.cpp** (called as a subprocess) using the `ggml-large-v3-turbo` model. A Silero VAD model is used to skip silent parts.
5. The raw transcript is then sent to **Ollama**, running the **Qwen 2.5 14B** model, which cleans it up (adds punctuation, fixes obvious errors, and formats it nicely).
6. The final transcript is written to `out/`, and the original file is moved to `done/`.

## Tools used

- Docker
- n8n
- Python 3.13 for the FastAPI
- ffmpeg
- whisper.cpp with the `ggml-large-v3-turbo.bin` model
- Ollama with the `qwen2.5:14b` model
