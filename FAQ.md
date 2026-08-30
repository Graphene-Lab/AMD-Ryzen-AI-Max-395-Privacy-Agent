# Frequently asked questions

## Installation

**Do I need to install .NET or Python?**
No. The release archive is self-contained: the runtime, the speech engine, the voices and every component are already inside (~700 MB compressed). The only system packages the installer needs (`curl`, `tar`, `libicu`) are installed automatically when missing.

**Which systems are supported?**
Any machine powered by the AMD Ryzen AI Max+ 395 processor — laptops, mini PCs, handhelds and workstations (the full device list is in the [README](README.md)) — running **Windows 11 (x64)** or a **64-bit Linux** distribution. The agent requires a 64-bit x86 system; it refuses to run on 32-bit systems.

**Can I install it without an internet connection on the machine?**
The one-line installer downloads the release from GitHub, so the machine needs internet for the first install. After that, with a local model (Ollama / ExLlamaV2), the assistant works fully offline.

**Where is it installed?**
Windows: `%LOCALAPPDATA%\AgentBridge`. Linux: `/opt/agentbridge` (change with `AGENTBRIDGE_HOME`). On Linux the service is called `agentbridge` and starts at boot.

**How do I update?**
The assistant checks for updates by itself (see the Updates guide). You can also re-run the installer — your documents, keys and settings are never touched.

## Usage

**How do I chat with it?**
The assistant is always on. On Linux, for the full-screen terminal chat run `sudo systemctl stop agentbridge` then `/opt/agentbridge/agent`; restart the service when done. On Windows, run `%LOCALAPPDATA%\AgentBridge\agent.exe`. The HTTP API on port 5290 is available at any time to scripts and bots.

**How do I choose which AI powers it?**
Type `/modelsetup` in the chat: pick a local model (everything stays on the machine) or a cloud provider (DeepSeek, Gemini, Anthropic) with anonymisation enabled by default.

**Where are my documents?**
The assistant reads the documents folder it indexes on first start and answers questions from your files. You can ask it for finished DOCX, XLSX, PPTX and PDF documents.

## Speech

**Does text-to-speech work on the AMD Ryzen AI Max+ 395?**
Yes — on Windows and Linux x64. The Kokoro neural voice, all voices and the ONNX model ship in the archive, and `/v1/audio/speech` produces WAV audio out of the box.

**Does speech recognition (dictation / voice calls) work?**
On Windows, yes. The speech-to-text component is currently built for Windows only; on Linux the voice endpoints report themselves unavailable until a Linux build is published. Everything else — including the assistant speaking its answers — works.

**Can the assistant answer phone calls?**
On Windows, yes (SIP). On Linux, the SIP bridge requires the speech-to-text component, so phone access is planned for a future Linux release. Telegram chat, in contrast, works fully.

## Privacy

**Do my documents leave the device?**
With a local model, nothing leaves the machine at all. With a cloud provider, the built-in anonymisation strips names and identifiers from the requests before they are sent.

**Who can access the assistant?**
By default the HTTP API listens on localhost. Telegram has an allow-list; SIP has a PIN. Expose it to your LAN only if you need to, and read the privacy guide for the details.

## Troubleshooting

**The service won't start.**
`journalctl -u agentbridge -n 30` shows the reason. The most common causes are a missing `libicu` (the installer installs it when it can) or a malformed `providers.json`/`appsettings.json`.

**Chat answers fail with "API key is not set".**
No provider is configured. Run `/modelsetup`, or add the provider in `providers.json` next to the executable.

**The installer says the architecture is unsupported.**
The archives are built for 64-bit x86. Check with `uname -m` — it must print `x86_64`.

**Do I need a monitor?**
No. On Linux, SSH into the machine and run the installer; on Windows, run the installer in a remote PowerShell session. The assistant is reachable through the API on port 5290 from anywhere on your network.
