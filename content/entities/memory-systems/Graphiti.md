---
title: Graphiti
type: entity
created: 2026-05-11
updated: 2026-05-11
sources:
  - https://github.com/getzep/graphiti
  - https://getzep.com
tags:
  - memory
  - agent
  - temporal
  - graph
---

# Graphiti

Build temporal context graphs for AI agents.

**GitHub**: [getzep/graphiti](https://github.com/getzep/graphiti) · ★25,876 · Apache 2.0 · Python

By **Zep** — the team behind one of the earliest production AI memory platforms. Graphiti is the open-source core of Zep's temporal knowledge graph technology.

## Core Innovation: Temporal Validity

Most knowledge graphs store *what is true*. Graphiti stores *when it was true and when it changed*. Every fact has a time window (valid_from → valid_until), and the graph tracks how relationships evolve over time.

This matters for agents in real-world domains:
- **Customer support**: "User had premium plan from Jan–Mar, switched to free in April"
- **Medical**: "Patient was on medication X until onset of symptoms Y"
- **Legal**: "Contract clause was amended in v2, superseded in v3"

Traditional RAG can't answer "what did the user think about X in March?" Graphiti can.

## Architecture

```
Input (chat, documents, events)
    → LLM Extraction (facts + time + entities)
    → Vertex/Edge Construction
    → Temporal Graph Store (Neo4j / FalkorDB / Kuzu)
    → Hybrid Retrieval (semantic + keyword + graph traversal)
    → Agent Context
```

### Key Components

- **Fact extraction**: LLM pulls structured facts with temporal bounds from unstructured input
- **Episode grouping**: Related facts grouped into episodes (conversational turns, events)
- **Temporal invalidation**: When contradictions are detected, old facts are closed with `valid_until` timestamps
- **Spanning tree retrieval**: Walk the graph forward/backward in time from a reference point

### Backends

- **Neo4j** (primary, recommended for production)
- **FalkorDB** (open-source Neo4j alternative)
- **Kuzu** (embedded graph DB)

### MCP Server

Graphiti ships an official MCP server (`mcp_server/`) so Claude, Cursor, and other MCP clients can query temporal context directly.

## Benchmarks

- **94.8% on Deep Memory Retrieval** benchmark
- SOC 2 Type II certified
- HIPAA compliant (enterprise)

## Deployment

```bash
# Python library
pip install graphiti-core[neo4j]

# Docker (with Neo4j)
docker run -p 7687:7687 -p 7474:7474 \
  -e NEO4J_AUTH=neo4j/password \
  neo4j:5.26

# MCP server
pip install mcp-server
python -m graphiti_mcp_server
```

## Comparison

| Feature | Graphiti | Mem0 | Letta |
|---------|----------|------|-------|
| Temporal modeling | ✅ Best-in-class | ❌ | ❌ |
| Graph storage | ✅ Neo4j | ✅ (Pro tier) | ❌ |
| Self-editing memory | ❌ | ❌ | ✅ |
| Open source | ✅ Apache 2.0 | ✅ (limited) | ✅ Apache 2.0 |
| Enterprise compliance | SOC 2, HIPAA | Partial | ❌ |

## Known Issues

- Neo4j dependency adds operational complexity
- Temporal fact extraction quality depends on LLM
- Not a complete agent platform — Graphiti is a retrieval layer, not a full memory system

## See Also

- [[Letta]] — full agent platform with self-editing memory
- [[Mem0]] — hybrid vector + graph, managed cloud
- [[Cognee]] — cognitive pipeline with enterprise focus
