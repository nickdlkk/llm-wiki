---
title: potpie
type: entity
source: github:potpie-ai/potpie
created: 2026-04-17
updated: 2026-04-17
tags:
  - knowledge-graph
  - codebase-indexing
  - claude-code
  - vscode-extension
---

# potpie

*"Turns your entire codebase into a knowledge graph."*

**Repo:** `potpie-ai/potpie` | **Lang:** Python | **License:** Apache 2.0 | **Status:** Active

---

## Summary

Potpie takes a fundamentally different approach to spec-driven development. Rather than adding a spec layer on top of coding, it builds a **knowledge graph from existing code** and uses that graph to make AI agents more precise.

**Core innovation:** Codebase-as-knowledge-graph. Instead of asking an AI to read millions of lines of code, potpie pre-indexes the codebase into a structural graph that AI can query with precision.

---

## Architecture

- **Indexing layer** — Scans codebase, extracts files, classes, functions, relationships
- **Knowledge graph** — Structured representation of code structure and dependencies
- **Agent layer** — AI agents query the graph for context, not the raw codebase
- **Interface** — VS Code extension + CLI

---

## Key Differentiator

Most SDD tools focus on *writing better specs*. Potpie focuses on *understanding existing code better*. It doesn't replace spec-driven development — it enables it on large, complex codebases where AI agents struggle to understand the full context.

---

## Strengths

- **Codebase comprehension at scale** — works on large existing codebases
- **Graph-based reasoning** — precision over fuzzy retrieval
- **Apache 2.0** — open source, no vendor lock-in
- **VS Code + CLI** — good developer experience

## Weaknesses

- Requires Docker (heavy dependency)
- Not a spec workflow tool — prerequisite tooling
- No built-in multi-agent orchestration
