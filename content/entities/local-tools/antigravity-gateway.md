---
title: Agent Vibes (Antigravity Gateway)
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/funny-vibes/agent-vibes
tags:
  - local-tools
  - gateway
  - AI agent
  - protocol translation
---

# Agent Vibes (Antigravity Gateway)

Agent Vibes is a **Unified Agent Gateway** that enables Claude Code CLI and Cursor IDE to use free AI backends (Antigravity, Codex) through protocol translation. It implements Cursor's native ConnectRPC/gRPC agent channel with full streaming tool loop while routing requests across Antigravity, Claude-compatible, Codex, and OpenAI-compatible backends.

## Features

- **Protocol Translation**: Bridges Claude Code CLI (Anthropic Messages API) and Cursor IDE (ConnectRPC/gRPC) to Antigravity and Codex backends
- **Multiple Backends**: Supports Antigravity IDE, Codex CLI, Claude-Compatible API, and OpenAI-compatible APIs
- **Streaming Tool Loop**: Full implementation of the streaming tool loop for agent interactions
- **Cursor ConnectRPC**: Native implementation of Cursor's ConnectRPC/gRPC agent channel

## Architecture

**Clients (front-end):**
- **Claude Code CLI** — Anthropic Messages API
- **Cursor IDE** — Protocol-compatible ConnectRPC/gRPC implementation

**Backends (back-end):**
- **Antigravity IDE** — Google Cloud Code API
- **Codex CLI** — OpenAI-compatible API
- **Claude-Compatible API** — Anthropic-compatible `/v1/messages`

## Tech Stack

- Node.js ≥24
- TypeScript 5
- NestJS 11
- Fastify HTTP/2

## Disclaimer

> This project is for educational and research purposes only. Using this proxy may put your Antigravity account at risk of being banned.

## Status

> [!WARNING]
> The **dev** branch is undergoing major refactoring based on Claude Code source architecture. Not recommended for production coding tasks.
