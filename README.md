# Conversational Audio Bot

A Streamlit app implementing a chained voice-assistant pipeline: **Whisper (speech-to-text) → GPT-4o (reasoning) → gTTS (text-to-speech)**.

## Prerequisites

- **Python 3.10+** installed and available on your system.
- **OpenAI API key** with access to the Whisper (`whisper-1`) and Chat Completions (`gpt-4o` / `gpt-4o-mini`) APIs.
- **ffmpeg** — required by audio libraries (`pydub`, `soundfile`) for audio decoding/encoding. A copy is bundled in the [ffmpeg/](ffmpeg/) folder of this repo, or install it system-wide:
  - Windows: `winget install ffmpeg` or download from [ffmpeg.org](https://ffmpeg.org/download.html) and add `bin/` to your `PATH`.
  - macOS: `brew install ffmpeg`
  - Linux: `sudo apt install ffmpeg`
- A working microphone (for `st.audio_input`) and a modern browser (Chrome/Edge recommended) to grant microphone permissions.

## Setup

### 1. Clone / copy the project

```powershell
cd D:\code\audio-bot-project\audio-bot-project
```

### 2. Create and activate a virtual environment

```powershell
python -m venv venv
```

Activate it (Windows PowerShell):

```powershell
.\venv\Scripts\Activate.ps1
```

> If you get an error like *"running scripts is disabled on this system"*, PowerShell's execution policy is blocking the activation script. Fix it for the current session only:
> ```powershell
> Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
> .\venv\Scripts\Activate.ps1
> ```
> Alternatively, skip activation entirely and call the venv's Python directly (see [Running the app](#running-the-app)).

macOS/Linux:

```bash
source venv/bin/activate
```

### 3. Install dependencies

```powershell
pip install -r requirements.txt
```

### 4. Configure your OpenAI API key

The app reads the key from the `OPENAI_API_KEY` environment variable.

Create a `.env` file in the project root (recommended, works with `python-dotenv`):

```
OPENAI_API_KEY=sk-your-key-here
```

Or set it directly in your shell for the current session (PowerShell):

```powershell
$env:OPENAI_API_KEY = "sk-your-key-here"
```

> **Never commit your API key.** Add `.env` to `.gitignore` if not already present.

## Running the app

This is a **Streamlit** app — it must be launched with `streamlit run`, not `python`, otherwise Streamlit runs in "bare mode" with no UI and you'll see `missing ScriptRunContext` warnings.

With the venv activated:

```powershell
streamlit run audio_app_em.py
```

Without activating the venv (direct call to the venv's Python):

```powershell
.\venv\Scripts\python.exe -m streamlit run .\audio_app_em.py
```

Streamlit will print a local URL, typically:

```
Local URL: http://localhost:8501
```

Open it in your browser, allow microphone access, and speak to interact with the assistant.

## Usage

1. Choose the LLM (`gpt-4o` or `gpt-4o-mini`), grounding temperature, and spoken language from the sidebar.
2. Click the microphone widget and record your speech.
3. The app transcribes your audio via Whisper, sends the text to GPT for a response, and reads the reply back using gTTS.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `missing ScriptRunContext!` warnings, no browser opens | Ran with `python audio_app_em.py` instead of `streamlit run` | Use `streamlit run audio_app_em.py` |
| `streamlit : not recognized` | venv not activated / not on PATH | Activate venv or call `venv\Scripts\python.exe -m streamlit run ...` |
| `running scripts is disabled on this system` | PowerShell execution policy blocks `.ps1` scripts | `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned` then re-activate |
| `AuthenticationError` from OpenAI | Missing/invalid `OPENAI_API_KEY` | Verify the env var/`.env` file is set and the key is valid |
| No audio input device found | Browser lacks mic permission or no mic present | Grant mic permission in browser settings; check OS audio input device |
