<p align="center">
  <img src="assets/nanoclaw-logo.png" alt="NanoClaw" width="400">
</p>

<p align="center">
  An always-on Claude assistant you can message from your phone. Runs on a <a href="https://sprites.dev">Sprite</a> cloud VM — no Mac Mini or home server required.
</p>

<p align="center">
  Fork of <a href="https://github.com/qwibitai/NanoClaw">NanoClaw</a>&nbsp; • &nbsp;
  <a href="https://discord.gg/VDdww8qS42"><img src="https://img.shields.io/discord/1470188214710046894?label=Discord&logo=discord&v=2" alt="Discord" valign="middle"></a>
</p>

## What is this?

[NanoClaw](https://github.com/qwibitai/NanoClaw) is a lightweight personal AI assistant — a single Node.js process that connects a messaging app to Claude agents running in isolated Docker containers. It's small enough to read and understand, and designed to be forked and customized.

This fork adds **Telegram** as the default channel and **[Sprite](https://sprites.dev)** as the hosting platform, so you can get an always-on Claude assistant without owning dedicated hardware. A Sprite is a persistent cloud Linux VM that sleeps when idle and wakes on demand — you only pay for what you use, and NanoClaw auto-restarts when the VM wakes.

## Quick Start

```bash
# Create a Sprite VM
sprite create nanoclaw
sprite console -s nanoclaw

# Inside the Sprite:
git clone https://github.com/jshph/nanoclaw.git ~/nanoclaw
cd ~/nanoclaw && claude
# Then run: /setup
```

Claude Code handles everything: Node.js, Docker, Telegram bot creation, container image build, and Sprite service registration. You'll just need to:

1. Paste your Claude API key or OAuth token
2. Create a Telegram bot via [@BotFather](https://t.me/BotFather) and paste the token
3. Send `/chatid` to your bot to register the chat

The whole process takes a few minutes.

## Why Sprite?

Running a personal AI assistant usually means a Mac Mini under your desk or a VPS you manage yourself. Sprite VMs are persistent Linux machines that **sleep when idle and wake on demand** — your bot stays reachable via Telegram even while the VM is suspended, and NanoClaw auto-restarts within seconds when a message arrives. No always-on hardware bill, no sysadmin work.

## How it works

NanoClaw is a single Node.js process. It connects to Telegram (or WhatsApp), stores messages in SQLite, and when triggered, spawns a Docker container running Claude Code via the Agent SDK. Each conversation group gets its own isolated container with its own filesystem and memory. The codebase is small enough to read end-to-end — fork it and make it yours.

## What you can do

Talk to your assistant from Telegram:

```
@Andy send an overview of the sales pipeline every weekday morning at 9am
@Andy review the git history for the past week each Friday and update the README if there's drift
@Andy every Monday at 8am, compile news on AI developments from Hacker News and TechCrunch
```

Each group gets isolated context, scheduled tasks, web access, and its own `CLAUDE.md` memory. Agents run sandboxed in Docker containers — they can only see what you explicitly mount.

## Customizing

The codebase is small enough that Claude can safely modify it. Just tell Claude Code what you want:

- "Change the trigger word to @Bob"
- "Make responses shorter and more direct"
- "Add a custom greeting when I say good morning"

Or run `/customize` for guided changes. See upstream [NanoClaw](https://github.com/qwibitai/NanoClaw) for the full list of available skills.

## Requirements

- A [Sprite](https://sprites.dev) account (or any Linux/macOS machine)
- [Claude Code](https://claude.ai/download)
- A Claude API key or Pro/Max subscription

Docker and Node.js are installed automatically during `/setup`.

## Architecture

```
Telegram (grammy) --> SQLite --> Polling loop --> Docker container (Claude Agent SDK) --> Response
```

Key files:
- `src/index.ts` - Orchestrator: state, message loop, agent invocation
- `src/channels/telegram.ts` - Telegram connection via grammy
- `src/container-runner.ts` - Spawns isolated agent containers
- `src/router.ts` - Message formatting and outbound routing
- `src/task-scheduler.ts` - Scheduled tasks
- `src/db.ts` - SQLite operations
- `groups/*/CLAUDE.md` - Per-group memory

## FAQ

**Do I need a Sprite?**

No. NanoClaw runs on any Linux or macOS machine. Sprite just makes it easy — no hardware to manage, and the VM sleeps when idle. If you have a Mac Mini or VPS, clone the [upstream repo](https://github.com/qwibitai/NanoClaw) and run `/setup` there instead.

**Is this secure?**

Agents run in Docker containers, not behind application-level permission checks. They can only access explicitly mounted directories. The codebase is small enough to audit yourself.

**How do I debug issues?**

Run `claude` in the project directory, then `/debug`. Claude reads the logs and fixes things.

## License

MIT
