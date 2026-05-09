---
title: claude-workflow
type: entity
source: github:sighup/claude-workflow
created: 2026-04-17
updated: 2026-04-17
tags:
  - claude-code
  - spec-driven
  - task-graph
  - multi-agent
  - worktree
---

# claude-workflow

*"Spec-driven development and autonomous task execution for Claude Code."*

**Repo:** `sighup/claude-workflow` | **Lang:** Python | **License:** MIT | **Status:** Active

---

## Summary

A Claude Code plugin that provides a complete SDD lifecycle: research → spec → Gherkin → plan → dispatch → validate. Parallel agent dispatch via `/cw-dispatch` with dependency-aware task graphs.

**Workflow chain:**
```
/cw-research → /cw-spec → /cw-gherkin → /cw-plan → /cw-dispatch → /cw-validate
```

---

## Key Commands

| Command | Purpose |
|---------|---------|
| `/cw-research` | Conduct research for a task |
| `/cw-spec` | Write structured specification |
| `/cw-gherkin` | Generate BDD scenarios |
| `/cw-plan` | Create dependency-aware task plan |
| `/cw-dispatch` | Dispatch parallel subagents |
| `/cw-worktree` | Create isolated worktree for parallel development |

---

## Architecture

- **Claude Code plugin** (Python backend + prompt conventions)
- **Task graph** — Dependency resolution between tasks
- **Worktree isolation** — Each parallel task in its own git worktree
- **Evidence-based validation** — Plan execution tracked with evidence

---

## Strengths

- Complete lifecycle (research → spec → validate)
- Parallel subagent dispatch
- Git worktree isolation prevents conflicts
- Gherkin/BDD integration

## Weaknesses

- Claude Code plugin only
- Python-only backend
