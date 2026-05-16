---
title: EdgeCrab (ACP UI)
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/raphaelmansuy/edgecrab
tags:
  - local-tools
  - terminal
  - AI agent
  - Rust
---

# EdgeCrab (ACP UI)

EdgeCrab is a **SuperAgent** — a personal assistant and coding agent built in Rust, inspired by NousHermes and OpenClaw. It provides a blazing-fast terminal UI with ReAct tool loop, multi-provider LLM support, ACP protocol, gateway adapters, and built-in security hardening.

## Features

- **Rust-native**: ~49 MB stripped release binary, zero Python or Node.js runtime dependencies
- **Multi-provider LLM**: 15 built-in LLM providers
- **Messaging platforms**: 17 gateway adapters for various messaging platforms
- **Cross-platform**: Runs on Linux, macOS, and Android (Termux)
- **ACP Protocol**: Implements the Agent Communication Protocol for multi-agent coordination
- **Shadow Judge**: Opt-in completion oracle for validating agent outputs
- **Harness signals**: Structured `report_task_status` signals for agent orchestration

## Architecture

```
hermes-agent soul  +  OpenClaw vision  =  EdgeCrab
   (reasoning)          (presence)        (Rust)
```

EdgeCrab combines the autonomous reasoning and persistent memory of hermes-agent with the always-on presence and messaging gateways of OpenClaw, packaged as a native Rust binary.

## Quick Start

```bash
# Install via npm (no Rust required)
npm install -g edgecrab-cli

# Or install via pip
pip install edgecrab-cli

# Run
edgecrab
```

## Related

- ACP protocol adapters for various messaging platforms
- Migrate from hermes-agent: `edgecrab migrate`
