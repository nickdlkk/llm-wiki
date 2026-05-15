---
title: Memori
type: entity
created: 2026-05-11
updated: 2026-05-11
sources:
  - https://github.com/MemoriLabs/Memori
  - https://memorilabs.ai
tags:
  - memory
  - agent
  - sql-native
---

# Memori

Agent-native memory infrastructure. Memory from what agents *do*, not just what they *say*.

**GitHub**: [MemoriLabs/Memori](https://github.com/MemoriLabs/Memori) · ★14,251 · Apache 2.0 · Python

## Core Philosophy: Do, Not Just Say

Most agent memory systems treat memory as derived from conversation text — the agent says something, and the system extracts facts. Memori takes a different stance: **memory should come from agent actions**, not just conversation.

When an agent reads a file, calls an API, or modifies state — that's also memory. Memori captures behavioral patterns, not just stated preferences.

## Architecture

SQL-native — memories stored in standard SQL tables, not vector stores. This is an intentional design choice:

- **Familiar tooling**: Query memory with normal SQL, visualize in any BI tool
- **No vector DB required**: Works with PostgreSQL, MySQL, SQLite
- **Structured facts**: NOT embeddings — actual structured data
- **LLM + datastore agnostic**: Doesn't dictate your stack

## Memory Types

1. **Episodic**: What happened in each session
2. **Semantic**: Extracted facts and knowledge
3. **Behavioral**: What the agent did (actions, tool calls, decisions)
4. **Relational**: Relationships between entities

## Deployment

```bash
# TypeScript SDK
npm install @memorilabs/memori

# Python SDK
pip install memori

# Docker self-hosted
docker run -p 8080:8080 memorilabs/memori
```

Cloud (memori.ai) and self-hosted options.

## Key Differentiators

| Feature | Memori | Mem0 | MemMachine |
|---------|--------|------|------------|
| Primary storage | SQL | Vector + Graph | SQL + vector |
| Source of truth | Actions + chat | Chat extraction | Chat extraction |
| LLM dependency | Low | High | Medium |
| Enterprise ready | Yes | Partial | No |
| Best for | Behavioral agents | Personal assistants | General agents |

## Known Issues

- SQL-native approach means semantic search quality depends on how well facts are structured (no vector similarity fallback)
- Smaller community than Mem0

## See Also

- [[MemMachine]] — also SQL-native, client-server model
- [[Mem0]] — vector + graph hybrid, most popular
- [[Cognee]] — cognitive pipeline, graph-first
