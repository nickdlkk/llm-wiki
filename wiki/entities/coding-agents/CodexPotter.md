---
title: CodexPotter
description: Multi-round autonomous agent runner — a Rust fork of OpenAI Codex that continuously reconciles codebases toward instructed target states via a Ralph Wiggum pattern
---

# CodexPotter

**Type:** Autonomous Agent Runner
**Website:** https://github.com/breezewish/CodexPotter
**GitHub:** https://github.com/breezewish/CodexPotter (545 stars)
**License:** Apache-2.0
**Created:** 2026-01-28
**Language:** Rust

## Overview

CodexPotter is a **multi-round autonomous agent runner** — a community fork of [OpenAI Codex](https://github.com/openai/codex) written in Rust. It continuously reconciles a codebase toward an instructed target state, following the **Ralph Wiggum pattern**: define the goal, and the agent works until the code matches the specification.

The key insight is a **Progress File** (`MAIN.md`) that defines the target state. CodexPotter drives Codex in multiple rounds, each time reading the progress file, having Codex work or review, and reconciling until alignment is achieved.

## Architecture

```mermaid
graph LR
    A["YOUR PROMPT<br/>Simplify the query engine by following..."] --> B[CodexPotter]
    B --> C["codex CLI<br/>Work or review according to MAIN.md"]
    C --> D[MAIN.md<br/>Progress File]
    D --> C
```

Each round uses a **clean context** to avoid context poisoning and maximize Codex's effective IQ. The agent stores instructions in files to resist context compaction and preserve all details across rounds.

## Key Features

- **Codex-first** — Requires only a Codex subscription; no extra LLM needed
- **Auto-review / reconcile** — Multi-round review and polish until fully aligned with instructions
- **Clean-room execution** — Fresh context each round, avoiding context pollution
- **Progress File** — `MAIN.md` defines target state; CodexPotter reconciles toward it
- **File system as memory** — Instructions persisted to files survive context compaction
- **Built-in knowledge base** — Local KB as index so Codex learns project fast in clean contexts
- **AGENTS.md / skills / MCPs** — Seamless integration with Codex ecosystem
- **Tiny footprint** — Uses <1k tokens in developer prompt
- **Resume support** — History replay + continue iterating from interrupted sessions

## Getting Started

```sh
# Install via npm
npm install -g codex-potter

# Run in your project (--yolo recommended for full autonomy)
codex-potter --yolo
```

**Note:** Unlike Codex, every follow-up prompt creates a **new task** — not sharing previous context. Assign tasks to CodexPotter, don't chat with it.

## Configuration

- `MAIN.md` in project root defines the target state (progress file)
- `docs/config.md` — Configuration options
- `docs/hooks.md` — Hook system for customization
- `--xmodel` — Use gpt-5.2 first, then gpt-5.5 to cross-review
- `/yolo` — Toggle YOLO mode (no sandbox) by default
- `/list` or `Ctrl+L` — View all projects and results

## Prompt Patterns

**✅ Works well:**
- Tasks with clear goals or scope: *"port upstream codex's /resume into this project"*
- Persist results to files for later rounds: *"create a design doc in DESIGN.md"*

**❌ Not suitable:**
- Interactive tasks with human feedback loops (use Codex directly)
- Front-end development with UI feedback
- Question-answering or brainstorming

## Technical Details

| Aspect | Detail |
|---|---|
| Language | Rust |
| Execution model | Loop executor — drives `codex` CLI |
| Context strategy | Clean context each round, file-system persistence |
| Memory | `MAIN.md` progress file + project KB |
| Platform | Linux, macOS, Windows |

## Related

- [OpenAI Codex](https://github.com/openai/codex) — upstream project
- [Harness Engineering](/wiki/concepts/Harness-Engineering) — context for agent reliability patterns
