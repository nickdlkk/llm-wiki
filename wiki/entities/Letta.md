---
title: Letta
type: entity
created: 2026-05-11
updated: 2026-05-11
sources:
  - https://github.com/letta-ai/letta
  - https://letta.com
tags:
  - memory
  - agent
  - llm-os
---

# Letta

Build AI with advanced memory that can learn and self-improve over time.

**GitHub**: [letta-ai/letta](https://github.com/letta-ai/letta) · ★22,604 · Apache 2.0 · Python

Formerly **MemGPT**. Rebranded to Letta to reflect a broader vision beyond memory.

## Core Philosophy: LLM-as-OS

Letta takes a fundamentally different approach to memory. Instead of a retrieval system that feeds context to the LLM, Letta gives the LLM **direct control over its own memory** — like giving a person a notebook and trusting them to take good notes.

The LLM explicitly reads and writes memory blocks via tool calls. Memory management becomes a first-class capability of the agent itself, not an external retrieval layer.

## Architecture

```
Letta Agent
├── Core Memory (human/persona blocks — always in context)
├── Archival Memory (searchable long-term storage)
├── Recall Memory (recent conversation history)
└── Moving Window (last N messages)
```

The agent decides when to archive, consolidate, or retrieve — same mental model as human memory management.

### Database Backends

- **SQLite + sqlite-vec** (default, local)
- **PostgreSQL + pgvector** (production)
- **Pinecone** (cloud vector)
- **Redis** (caching layer)

### Personas

Letta ships with built-in personas for memory management:

- `sleeptime_memory_persona` — Consolidates memories into concise blocks, identifies patterns, makes inferences
- `voice_memory_persona` — Archives important dialogue, maintains up-to-date user profile

## Benchmarks

Letta/MemGPT was the first system to demonstrate meaningful multi-session memory on standard benchmarks. On the *Stanford LoCoMo* benchmark, Letta performs competitively (Letta's Neuromancer XR = 86.9% on LoCoMo, per Honcho integration).

## Deployment

```bash
# CLI (Letta Code)
npm install -g @letta-ai/letta-code
letta

# Python server
pip install letta[server]
letta run server

# Docker
docker run -p 8283:8283 letta/letta
```

Managed cloud at [app.letta.com](https://app.letta.com) with API key auth.

## Key Differentiators

| Feature | Letta | Mem0 | Supermemory |
|---------|-------|------|-------------|
| Memory control | LLM self-managed | Platform managed | Platform managed |
| Memory type | Block-based | Vector + Graph | Vector + semantic |
| Local部署 | ✅ SQLite | ✅ Docker | Cloud-only |
| MCP support | ✅ | ✅ | ✅ |
| Multi-agent | ✅ | ✅ | ✅ |

## Known Issues

- Every memory operation costs tokens (LLM decides what to write)
- Model can drift over time as it edits its own state
- Memory quality depends heavily on persona design
- Self-hosting requires PostgreSQL for production

## See Also

- [[Mem0]] — platform-managed memory, not LLM self-editing
- [[Supermemory]] — #1 on LoCoMo, hybrid memory + RAG
- [[Honcho]] — Letta-based agent memory management for Hermes
