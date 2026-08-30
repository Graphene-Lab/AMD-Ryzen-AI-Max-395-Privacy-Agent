# Getting started with AgentBridge

AgentBridge is your own AI assistant. It runs on your computer, reads your documents, and helps you with everyday office work: drafting letters, building spreadsheets, answering questions about your files, and much more. Everything happens on your machine, so your documents stay private. You talk to it in plain language, the way you would write to a colleague, and it takes care of the rest.

## Downloading AgentBridge

Getting AgentBridge on your AMD Ryzen AI Max+ 395 machine is a single copy-paste command:

**Windows (PowerShell):**

```powershell
irm https://graphenelab.it/AgentBridge/install.ps1 | iex
```

**Linux (x86_64):**

```bash
curl -fsSL https://graphenelab.it/AgentBridge/install.sh | bash
```

The installer downloads the latest release for your platform, unpacks it into `%LOCALAPPDATA%\AgentBridge` (Windows) or `/opt/agentbridge` (Linux), installs any missing system dependency and — on Linux — registers the assistant as a service that starts automatically at boot. You can also download the package manually from the [AgentBridge releases page](https://github.com/Graphene-Lab/AgentBridge/releases) if you prefer.

The package is self-contained, which means you do not need to install anything else. The program itself, the voices used for speech, and every other component the assistant needs are already inside the package, about 700 megabytes in total.

## Starting AgentBridge

After the download finishes, extract the archive into a folder of your choice. On Windows, start the program by opening the file named agent.exe; on Linux or macOS, run the file named agent. Nothing else is required.

When AgentBridge starts, you will see a full-screen chat window, similar to a messaging application. At the same time, a small local server starts on your machine that other programs can use to speak with the same assistant. You do not need to worry about this detail, because everything works together automatically.

## The first start

The first time you start AgentBridge, the assistant begins reading your documents folder in the background. If you have a large collection of files, this first indexing can take a few minutes, but you can start chatting right away while it works. From that moment on, the assistant can answer questions using the information found in your own files, instead of guessing.

## What happens next

The assistant is ready when you are. Type your first request in plain language and see what it can do: ask it to write a letter, summarise a report, or prepare a spreadsheet. AgentBridge also keeps itself up to date automatically, and an update never touches your documents, your keys, or your settings.

The next guide in this series explains how to choose the artificial intelligence that powers your assistant and how to connect your favourite provider.
