# Installation

This page explains how to install the **AMD Ryzen AI Max+ 395 Privacy Agent** on a **Windows 11** or **Linux (x86_64)** machine powered by the **AMD Ryzen AI Max+ 395** processor, what you need before you start, and how to verify that everything works. The install is a single copy-paste command: no .NET runtime, no extra setup, no manual dependency handling. The agent downloads and publishes its releases from the [AgentBridge](https://github.com/Graphene-Lab/AgentBridge) repository — this project does not host separate releases.

## Prerequisites

| Requirement | Details |
|---|---|
| **Processor** | AMD Ryzen AI Max+ 395 (16-core x86-64) — any device powered by it, see the [device list](README.md) |
| **OS** | **Windows 11 (x64)** or a **64-bit Linux** distribution (Debian/Ubuntu-based). Linux installs use systemd and `apt` where available |
| **Storage** | At least **1.5 GB free** (the release archive is ~460 MB, the installed app ~810 MB) |
| **Memory** | 16 GB or more recommended |
| **Network** | Internet access to download the release (GitHub). The assistant itself can later run fully offline with a local model |
| **Access** | On Linux: a terminal with `sudo` rights. On Windows: a PowerShell console |

## One-line install

**Windows — PowerShell:**

```powershell
irm https://graphenelab.it/AgentBridge/install.ps1 | iex
```

The installer downloads the latest **Windows x64** release, unpacks it into `%LOCALAPPDATA%\AgentBridge` and prints how to start the agent (`agent.exe`). The TTS engine automatically detects a CUDA-capable NVIDIA GPU and offers to install the CUDA Toolkit for ~1.5× faster speech; otherwise it runs on the CPU.

**Linux (x86_64):**

```bash
curl -fsSL https://graphenelab.it/AgentBridge/install.sh | bash
```

What the Linux installer does automatically:

1. Detects the platform (x86_64 or arm64) and installs `curl`, `tar` and `libicu` if they are missing.
2. Downloads the **latest** AgentBridge `linux-x64` release from GitHub (pin a version with `AGENTBRIDGE_VERSION=v1.26.08.30`).
3. Unpacks it directly into `/opt/agentbridge` and fixes the file ownership.
4. Creates and starts a **systemd service** (`agentbridge`) that keeps the assistant always on and starts it at boot.
5. Prints the final status.

### Options (Linux)

```bash
# Install a specific version instead of latest
curl -fsSL https://graphenelab.it/AgentBridge/install.sh | \
  AGENTBRIDGE_VERSION=v1.26.08.30 bash

# Install into a custom folder (no systemd service)
curl -fsSL https://graphenelab.it/AgentBridge/install.sh | \
  AGENTBRIDGE_HOME=/home/you/agent AGENTBRIDGE_NO_SERVICE=1 bash
```

## Direct download

Prefer a manual install? Download the right archive from the [AgentBridge releases page](https://github.com/Graphene-Lab/AgentBridge/releases/latest):

- **Windows x64:** `agentbridge-win-x64.tar.gz`
- **Linux x64:** `agentbridge-linux-x64.tar.gz`

Both are self-contained (~460 MB): no .NET installation, Kokoro TTS voices included. Extract and run `agent.exe` (Windows) or `agent` (Linux).

## First start

After the install the assistant is already running. Verify it:

```bash
curl -s http://localhost:5290/health
# -> {"status":"healthy",...}

curl -s http://localhost:5290/v1/models
# -> lists the agents and the configured AI providers
```

**Terminal chat (TUI):**

- **Linux** — the service owns port 5290, so for the interactive chat run `sudo systemctl stop agentbridge`, then `/opt/agentbridge/agent`; restart the service when done.
- **Windows** — run `%LOCALAPPDATA%\AgentBridge\agent.exe` directly.

**API access:** the assistant answers any OpenAI-compatible call on `http://<machine-ip>:5290` (e.g. `/v1/chat/completions`, `/v1/audio/speech`) — scripts, bots and the web client all use the same server.

## Choosing the AI that powers the device

Type `/modelsetup` in the chat and pick a provider:

- **Local model** (fully offline): Ollama or ExLlamaV2 on the machine itself.
- **Cloud provider**: DeepSeek, Gemini, Anthropic — with the built-in GDPR-ready anonymisation that strips names and identifiers before any request leaves the device.

You can also edit `providers.json` next to the executable directly (it is the single source of truth for API keys) — see the AgentBridge documentation.

## Speech: what works out of the box

- **Text-to-speech (TTS)** — yes, on both Windows and Linux: the Kokoro neural voice, its voices and the ONNX model are shipped in the archive. On Windows, the TTS uses the GPU automatically when a CUDA-capable NVIDIA GPU and the CUDA Toolkit are present. Test it:

  ```bash
  curl -s -X POST http://localhost:5290/v1/audio/speech \
    -H 'Content-Type: application/json' \
    -d '{"model":"kokoro","input":"Hello from the AMD Ryzen AI Max+ 395.","voice":"af_heart"}' \
    -o hello.wav && file hello.wav
  # -> RIFF ... WAVE audio, 16 bit, mono 24000 Hz
  ```

- **Speech-to-text (dictation / voice calls)** — works on **Windows** (the STT engine `voiceagent-stt` is built for Windows). On Linux it is not yet shipped: the `/v1/voice/listen` endpoint reports itself unavailable until a Linux build of the STT component is published. Text chat, documents, email, research, Telegram, scheduling and podcasts work on both platforms.

## Updating

The assistant updates itself automatically in the background (see the Updates guide). To force a fresh install of a newer release, re-run the same one-line installer for your OS. Your documents, keys and settings are never touched.

## Uninstalling

**Linux:**

```bash
sudo systemctl stop agentbridge
sudo systemctl disable agentbridge
sudo rm -f /etc/systemd/system/agentbridge.service
sudo systemctl daemon-reload
sudo rm -rf /opt/agentbridge
```

**Windows:** close the agent, then delete the `%LOCALAPPDATA%\AgentBridge` folder.

## Troubleshooting

| Problem | Fix |
|---|---|
| `unsupported architecture` | The Linux installer runs on 64-bit (`x86_64`) — check with `uname -m` |
| Service does not start (Linux) | `systemctl status agentbridge` and `journalctl -u agentbridge -n 30` for the error; common causes: missing `libicu`, or an edited config file with a syntax error |
| Port 5290 already in use | Stop the other instance, or change `"Urls"` in `appsettings.json` next to the executable and restart |
| Chat answers fail with "API key is not set" | No provider is configured yet — run `/modelsetup` in the chat, or add the provider in `providers.json` |
| Chat answers fail with an error about a local bridge | Providers on the same LAN are supported since AgentBridge v1.26.08.30 (they need no API key); point `providers.json` at the machine that runs the bridge (e.g. `http://192.168.x.x:8787/`) |
