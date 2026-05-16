---
title: Codeg
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/xintaofei/codeg
tags:
  - local-tools
  - IDE
  - AI coding workspace
  - multi-agent
---

# Codeg

Codeg (Code Generation) is an enterprise-grade multi-agent coding workspace that unifies local AI coding agents (Claude Code, Codex CLI, OpenCode, Gemini CLI, OpenClaw, Cline, etc.) in a single desktop app, standalone server, or Docker container.

## Features

- **Unified Multi-Agent Workspace**: Manage multiple AI coding agents in the same project
- **Local Conversation Ingestion**: Structured rendering of agent conversations
- **Parallel Development**: Built-in `git worktree` flows for parallel work
- **Project Boot**: Visually scaffold new projects with live preview
- **Chat Channels**: Connect Telegram, Lark (Feishu), iLink (Weixin) for real-time notifications and remote task control
- **MCP Management**: Local scan + registry search/install
- **Skills Management**: Global and project scope
- **Git Integration**: Remote account management (GitHub and other Git servers)
- **Web Service Mode**: Access Codeg from any browser for remote work

## Screenshots

![Codeg Light](./docs/images/main-light.png)
![Codeg Dark](./docs/images/main-dark.png)

## Tech Stack

- Tauri 2.x (desktop framework)
- Next.js 16
- Docker-ready

## Quick Start

```bash
# Desktop app - download from releases
# Or Docker
docker run -p 3000:3000 codeg
```
