---
title: Mem0
type: entity
created: 2026-05-10
updated: 2026-05-11
sources:
  - https://github.com/mem0ai/mem0
  - https://mem0.ai
tags:
  - memory
  - agent
  - platform
---

# Mem0

Universal memory layer for AI Agents.

**GitHub**: [mem0ai/mem0](https://github.com/mem0ai/mem0) · ★55,298 · Apache 2.0 · Python

> **Note**: The `mem0ai/mem0` repo now serves the Supermemory project. The Mem0 brand/website has evolved — the core open-source memory technology lives on in both Mem0 and Supermemory products.

## Core Value

Mem0 coined the "memory layer for AI agents" category. The key insight: LLMs are stateless between sessions, and naive RAG can't capture user preferences, conversation context, or evolving knowledge.

Mem0 provides structured, multi-level memory:

- **User-level**: Global preferences and facts about the user
- **Session-level**: Per-conversation context
- **Agent-level**: Cross-session agent knowledge

## Architecture

Vector search + knowledge graph hybrid. Facts are stored as entities with relationships, enabling multi-hop queries like "what does the user think about X?" or "what have we discussed about Y?".

Key technical features:
- **Intelligent compression**: 90%+ token savings vs raw context (from Mem0 research)
- **Embedding configurability**: Plug in OpenAI, Cohere, or custom embedders
- **API-first**: REST API + Python/TypeScript SDKs
- **Managed cloud**: mem0.ai hosted platform (186M+ monthly API calls)
- **Self-hosted**: Bring your own vector DB (Qdrant, Pinecone, pgvector, Chroma)

## Benchmarks

Mem0's research paper reports **26% improvement over OpenAI's memory** baseline with 90%+ token savings.

## Deployment

| Option | Description |
|--------|-------------|
| **Managed cloud** | mem0.ai — sign up, get API key, done |
| **Self-hosted** | Docker compose with your own vector DB |
| **Embedded** | pip install, in-process with SQLite |

## Ecosystem

- Python SDK: `pip install mem0ai`
- TypeScript SDK: `npm install @mem0-ai/mem0`
- MCP server: `@mem0-ai/mcp-server`
- LangChain integration
- Vercel AI SDK wrapper

## Limitations

- Graph features (entity relationships, multi-hop) require **Pro tier** ($249/month)
- Standard tier ($19/month) is vector search only
- Fast growth has led to some platform reliability issues

## See Also

- [[Supermemory]] — evolved from same memory research, TypeScript-first, #1 on LoCoMo
- [[Letta]] — LLM-as-OS approach, model manages own memory
- [[Graphiti]] — Zep's temporal knowledge graph
