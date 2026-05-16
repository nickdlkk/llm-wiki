---
title: opencode-remote-control
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/bevibing/remote-opencode
tags:
  - local-tools
  - Discord bot
  - OpenCode
  - remote access
---

# opencode-remote-control (remote-opencode)

**Control your AI coding assistant from anywhere** — your phone, tablet, or another computer via Discord.

## Features

- **📱 Code from mobile**: Send coding tasks from your phone while away from your desk
- **💻 Access from any device**: Use your powerful dev machine from a laptop or tablet
- **🌍 Work remotely**: Control your home/office workstation from anywhere
- **👥 Collaborate**: Share AI coding sessions with team members in Discord
- **🤖 Automated Workflows**: Queue up multiple tasks and let the bot process them sequentially
- **🎤 Voice Messages**: Send voice messages that are automatically transcribed

## Architecture

```
📱 Your Phone / Other Device ←→ Discord API ←→ 🤖 Discord Bot
                                        ↓
                                  💻 OpenCode CLI
                                        ↓
                                  📁 Your Codebase
```

## Discord Slash Commands

| Command | Description |
|---------|-------------|
| `/opencode` | Send command to AI, streams response |
| `/work` | Create Git worktree for isolated work |
| `/code` | Toggle passthrough mode |
| `/diff` | View git diffs |
| `/session` | Browse & manage sessions |
| `/queue` | Manage message queue |
| `/model` | List & set AI model |
| `/setpath` | Register a project path |
| `/use` | Bind project to channel |

## Installation

```bash
# Global installation
npm install -g remote-opencode

# Or run directly
npx remote-opencode
```

## Quick Start

```bash
remote-opencode setup
remote-opencode start
```

## Access Control

Optional user allowlist restricts bot access to authorized Discord users only.
