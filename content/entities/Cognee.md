---
title: Cognee
type: entity
created: 2026-05-11
updated: 2026-05-11
sources:
  - https://github.com/topoteretes/cognee
  - https://cognee.ai
tags:
  - memory
  - agent
  - pipeline
  - cognitive
---

# Cognee

Memory control plane for AI agents. Six lines of code to add memory to any agent.

**GitHub**: [topoteretes/cognee](https://github.com/topoteretes/cognee) · ★17,148 · Apache 2.0 · Python

## Core Philosophy

Cognee implements a **cognitive science-inspired pipeline** for converting raw, messy real-world data into structured agent memory. The name is a play on "cognition" + "cognee" (French: "crank" — the mechanism that converts rotation into motion).

The insight: most data sources (Slack threads, emails, meeting notes, PDFs) don't arrive as clean facts. Cognee's pipeline forces structure through a rigorous extraction process before committing to storage.

## Six-Stage Pipeline

```
1. Classify    → What type of data is this? (conversation, document, email, etc.)
2. Chunk        → Split into meaningful units
3. Extract      → Pull entities (who, what, when, where)
4. Summarize    → Generate condensed representation
5. Embed        → Vectorize for semantic search
6. Commit       → Store in graph (and optionally vector store)
```

Each stage is a discrete LLM call, making it easy to inspect, debug, and swap implementations.

## Architecture

Cognee is **graph-first** — facts go into a knowledge graph, not just a vector store. The graph enables:
- Multi-hop reasoning ("what projects is Alice working on?")
- Attribution ("which document said X?")
- Temporal tracking (via custom extension)

Supported storage backends:
- **NetworkX** (in-memory, default)
- **Neo4j** (production graph)
- **pgvector** (vector search)
- **Qdrant**, **Chroma**, **LanceDB** (via langchain)

## Key Strengths

| Capability | Detail |
|------------|--------|
| **Data sources** | 38+ connectors: Slack, Notion, GitHub, email, PDFs, videos, etc. |
| **Enterprise** | Adopted by 70+ companies including Bayer |
| **Funding** | $7.5M raised (backers include OpenAI founders, FAIR) |
| **Pipeline** | Modular — swap any stage with custom implementation |
| **Graph + Vector** | Both stored simultaneously, dual retrieval |

## Benchmarks

Cognee doesn't publish benchmark numbers. The team focuses on production reliability over benchmark chasing.

## Deployment

```bash
pip install cognee

# 6 lines to memory
import cognee

cognee.add("User prefers dark mode and Vim")
cognee.add("Working on API integration project")

results = await cognee.search("What does the user prefer for coding?")
```

Self-hosted or use cognee Cloud (managed).

## Comparison

| Feature | Cognee | Mem0 | Graphiti |
|---------|--------|------|----------|
| Pipeline stages | 6 (classify→commit) | Hybrid (embedding + graph) | Temporal extraction |
| Data sources | 38+ built-in | API + basic | Manual ingestion |
| Graph type | Generic knowledge | Entity graph | Temporal validity |
| Open source | Full Apache 2.0 | Partial (tiers) | Full Apache 2.0 |
| Best for | Enterprise data pipelines | Agent memory | Time-sensitive domains |

## See Also

- [[Graphiti]] — temporal knowledge graphs, time-aware retrieval
- [[Mem0]] — simpler memory layer, managed platform
- [[Supermemory]] — hybrid memory + RAG, benchmark leader
