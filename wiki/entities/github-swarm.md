---
title: claude-swarm
type: entity
source: github:fntune/swarm
created: 2026-04-17
updated: 2026-04-17
tags:
  - claude-code
  - multi-agent
  - worktree
  - orchestration
  - parallel
  - crash-recovery
---

# claude-swarm

*"Multi-agent orchestration for Claude Code. Parallel worktrees. Resumable runs."*

**Repo:** `fntune/swarm` | **Lang:** Python | **License:** Apache 2.0 | **Status:** Active

---

## Summary

Swarm executes AI agents in parallel git worktrees with dependency resolution, retry logic, circuit breakers, and crash recovery. Plans are declared in YAML — `swarm run -f plan.yaml` orchestrates everything.

**Key differentiator:** Resume-first design. `swarm resume <run-id>` recovers from crashes mid-run.

---

## Core Commands

```bash
swarm run -f plan.yaml              # Execute plan
swarm run -p "auth: Implement auth" # Inline single agent
swarm run --sequential              # Linear pipeline mode
swarm resume <run_id>              # Recover from crash
swarm status <run_id>              # View status
swarm logs <run_id> -a <agent>     # View agent logs
swarm merge <run_id>               # Merge completed branches
swarm cancel <run_id>              # Cancel running agents
swarm dashboard <run_id>           # Live status view
```

---

## Architecture

```
models.py     → Pydantic: AgentSpec, PlanSpec, Defaults
parser.py     → YAML parsing, inline plan creation
deps.py       → Dependency graph, topological sort
scheduler.py  → Parallel execution, circuit breaker, stuck detection
executor.py   → Agent execution (SDK + MCP tools)
git.py        → Worktree creation, branch merging
merge.py      → Branch consolidation, conflict resolution
db.py         → SQLite (WAL mode) for crash recovery
```

---

## Failure Handling

| Mode | Behavior |
|------|----------|
| `continue` | Default — continue with other agents on failure |
| `stop` | Cancel all agents on first failure |
| `retry` | Retry failed agents up to `retry_count` times |

**Circuit breaker:** Trip after N failures → `cancel_all` / `pause` / `notify`

---

## Built-in Roles

architect, implementer, tester, reviewer, debugger, refactorer, documenter

---

## Strengths

- **Crash recovery** — SQLite state + resume survives mid-run crashes
- **Parallel execution without conflicts** — git worktree isolation
- **YAML plan format** — declarative, version-controllable
- **Circuit breaker** — prevents cascading failures
- **Resume-first** — no work lost on crash

## Weaknesses

- No built-in spec format (assumes user provides plan YAML)
- Requires Claude SDK
- No built-in human approval gates
