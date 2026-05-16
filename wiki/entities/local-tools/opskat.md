---
title: OpsKat
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/opskat/opskat
tags:
  - local-tools
  - infrastructure
  - DevOps
  - AI agent
---

# OpsKat

**Infrastructure Ops, Reimagined with AI** — An open-source, AI-first desktop application for managing remote infrastructure.

## Features

- **AI Agent**: Describe what you need in natural language, and the AI handles the rest
- **Policy Enforcement**: Full audit logging for all operations
- **Unified Interface**: SSH servers, MySQL/PostgreSQL databases, Redis, MongoDB, and Kafka in one place
- **Plugin System**: Extensible asset types via plugins

## Supported Assets

- SSH Servers
- MySQL/PostgreSQL Databases
- Redis
- MongoDB
- Kafka
- More via plugin system

## Example Use Cases

- **"Show me the recent nginx error logs on web-01"** → AI automatically SSHs in, runs the command, and returns the results

## Tech Stack

- Go 1.25
- React 19
- Wails v2
- Cross-platform (macOS, Linux, Windows)

## Installation

Download from [opskat.dev](https://opskat.dev/) or GitHub Releases.
