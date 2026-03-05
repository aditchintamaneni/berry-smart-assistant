# Meet the Berry Smart Assistant!

A high-performance voice AI assistant optimized for edge deployment on a Raspberry Pi 5 (4GB RAM). Runs entirely on-device (no cloud) and achieves ~2.5 second response latency.

## What it does

1. wakes up on "Hey Jarvis" using OpenWakeWord
2. listens to you via faster-whisper (Speech-to-Text)
3. thinks via Gemma3:1B running locally through Ollama
4. speaks response via Piper (Text-to-Speech)

## What makes it fast

Under the hood, responses stream token-by-token and are spoken sentence-by-sentence, significantly reducing perceived latency. The system is fully multithreaded: wake word detection, the conversation pipeline, timer monitoring, and interrupt detection all run concurrently.

## Features
* you can interrupt Jarvis mid-response. It dynamically adjusts voice activity detection based on ambient noise, so it works in any environment.
* handles timers and alarms with regex parsing that accounts for faster-whisper transcription quirks
* maintains up to 3 conversation turns per interaction so dialogue feels natural
* custom Timer class (decorator and context manager) for logging latency across each pipeline stage

## Tech Stack
| Component | Tool |
|---|---|
| Speech-to-Text | faster-whisper (tiny.en) |
| Language model | Gemma3:1B (quantized) via Ollama |
| Text-to-Speech | Piper TTS |
| Wake word | OpenWakeWord |
| Audio capture | PyAudio (16kHz mono) |
| Concurrency | Python threading with Events and Locks |

## Setup

**Prereqs:**
- Raspberry Pi 5 (4GB+ RAM) or any Linux machine
- USB speakerphone (or separate mic + speaker)
- Python 3.11+

**Install dependencies:**
```bash
sudo apt update && sudo apt install -y portaudio19-dev
pip install -r requirements.txt
```

**Install Ollama and pull the model:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama pull gemma3:1b-it-qat
```

**Install Piper TTS:**
```bash
pip install piper-tts
```

**Download a Piper voice:**
```bash
mkdir -p voices
# download from https://github.com/rhasspy/piper/blob/master/VOICES.md
# place the .onnx and .onnx.json files in the voices/ directory
# default voice: en_US-amy-low.onnx
```

## Usage

```bash
# continuous conversation mode (default)
python main.py

# wake word mode — waits for "Hey Jarvis" before listening
python main.py --wake-word
```
