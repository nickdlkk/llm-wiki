---
title: MemMachine
type: entity
created: 2026-05-11
updated: 2026-05-11
sources:
  - https://github.com/MemMachine/MemMachine
  - https://memmachine.ai
tags:
  - memory
  - agent
  - universal
---

# MemMachine

Universal memory layer for AI Agents — open-source, scalable, and interoperable.

**GitHub**: [MemMachine/MemMachine](https://github.com/MemMachine/MemMachine) · ★3,067 · Apache 2.0 · Python

## Core Value

MemMachine's pitch is interoperability and generality. Where other memory systems lock you into their storage backend or cloud, MemMachine aims to be the "universal memory adapter" — a standard interface that works with any storage backend.

The three-layer memory model:

- **Episodic Memory**: Graph-based conversational context, persists across sessions
- **Profile Memory**: Long-term user facts stored in SQL (structured, not vector)
- **Working Memory**: Short-term session context

## Architecture

```
MemMachine Client (your agent)
    → MemMachine Server (REST API, Docker)
        → Storage Backend (SQLite, PostgreSQL, your DB)
```

Client-server design means your agent doesn't bundle memory logic — it talks to a server over HTTP. The server handles storage, retrieval, and deduplication.

## Quick Start

```bash
# Docker (server)
docker run -p 8080:8080 memmachine/server

# Python client
pip install memmachine-client

from memmachine_client import MemMachineClient
client = MemMachineClient(base_url="http://localhost:8080")
memory = client.get_or_create_project(org_id="my_org").memory(
    group_id="default",
    agent_id="my_agent"
)
memory.add("User prefers TypeScript over Python")
results = memory.search("What does the user prefer?")
```

## Deployment

| Option | Description |
|--------|-------------|
| **Docker** | `docker run memmachine/server` — self-contained |
| **Managed cloud** | console.memmachine.ai |
| **pip** | pip install memmachine-client (bring your own server) |

## Key Differentiators

- **Universal**: One client interface, multiple backend storage options
- **SQL Profile Memory**: Structured facts in SQL (not just embeddings)
- **Cross-agent**: Shared memory across different agent instances
- **Model-agnostic**: Works with any LLM

## Comparison

| Feature | MemMachine | Mem0 | Letta |
|---------|------------|------|-------|
| Architecture | Client-server | SDK + API | Full agent platform |
| Profile storage | SQL (structured) | Vector + Graph | Blocks |
| Storage backends | Any SQL + vector | Multiple | SQLite/PG/Pinecone |
| Open source | Full Apache 2.0 | Partial | Full Apache 2.0 |
| Stars | ★3k | ★55k | ★22k |

## Known Issues

- Smaller community vs Mem0/Letta
- Client-server model adds latency vs in-process libraries
- Documentation is thinner than competitors

## See Also

- [[Mem0]] — most popular open-source memory layer
- [[Letta]] — full agent platform with self-management
- [[Memori]] — also SQL-native, different design philosophy
