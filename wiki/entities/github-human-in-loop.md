---
title: HumanInLoop (HIL)
type: entity
source: github:deepeshBodh/human-in-loop
created: 2026-04-17
updated: 2026-04-17
tags:
  - claude-code
  - spec-driven
  - human-in-the-loop
  - mcp
  - deterministic
---

# HumanInLoop (HIL)

*"SPEC-first multi-agent framework for Claude Code with deterministic DAG infrastructure."*

**Repo:** `deepeshBodh/human-in-loop` | **Lang:** Python | **License:** MIT | **Status:** Active

---

## Summary

HumanInLoop combines SPEC-first development with deterministic DAG infrastructure. Human review checkpoints occur at every workflow transition — architectural decisions are made by humans before AI writes code.

**Workflow:**
```
SETUP → SPECIFY → PLAN → TASKS → IMPLEMENT → AUDIT (review)
```

---

## Architecture

**Core package:** `humaninloop_brain` — Python package with strict layer dependency:
```
entities → graph → validators → passes → mcp → cli
```

- **381+ tests** with 90% coverage gate (blocking CI)
- **8 ADRs** documenting architectural decisions
- **MCP server** (`hil-dag`) — sole write gate for StrategyGraph JSON
- **Constitution** (`.humaninloop/memory/constitution.md`) — project governance

---

## Key Principles

| Principle | Description |
|-----------|-------------|
| Deterministic Infrastructure | Graph operations in `humaninloop_brain`, not in agent prompts |
| Human checkpoints | Every workflow transition requires human approval |
| Layer dependency | No upward imports in the Python package |
| Pydantic entity modeling | Frozen models, 11 enums, 14 models |
| Conventional commits | `type(scope): description` with pre-commit + CI enforcement |

---

## Strengths

- Human-in-the-loop enforcement is explicit and enforced
- Deterministic infrastructure (testable, no prompt dependency)
- High quality bar (90% coverage gate)
- Well-documented constitution and ADRs

## Weaknesses

- Heavy infrastructure for simple projects
- Python-only backend
- Complex setup for small teams
