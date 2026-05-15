---
title: "Symphony"
type: entity
created: 2026-05-15
updated: 2026-05-15
sources: ["raw/refs/symphony/README.md", "raw/refs/symphony/SPEC.md", "raw/refs/symphony/elixir/README.md"]
tags: [openai, orchestration, linear, codex, elixir, OTP, agent-swarm, workflow-orchestration]
---

# Symphony

> GitHub: [openai/symphony](https://github.com/openai/symphony)
> Apache License 2.0
> Stars: 23.8k · Forks: 2.3k

**AI programming agent orchestration system** — continuously polls a Linear issue tracker, creates isolated per-issue workspaces, and drives Codex App Server sessions to complete work autonomously. Teams manage work instead of supervising agents.

## Overview

Symphony is a language-agnostic **specification** (`SPEC.md`) plus an **Elixir/OTP reference implementation**. The spec defines a durable orchestration protocol; the Elixir code is a prototype for evaluation.

> [!WARNING]
> Symphony is a low-key engineering preview for testing in trusted environments.

Core workflow:
1. Poll Linear for issues in active states on a fixed cadence
2. Create an isolated workspace per issue
3. Launch Codex in [App Server mode](https://developers.openai.com/codex/app-server/) inside the workspace
4. Send a `WORKFLOW.md` prompt to Codex
5. Keep Codex working until the issue reaches a terminal state

If a claimed issue moves to a terminal state (`Done`, `Closed`, `Cancelled`, `Duplicate`), Symphony stops the active agent and cleans up the workspace.

## Architecture

### Six Abstraction Layers

```
┌─────────────────────────────────────────────────────┐
│ 1. Policy Layer (repo-defined WORKFLOW.md)          │
├─────────────────────────────────────────────────────┤
│ 2. Configuration Layer (typed getters from YAML)    │
├─────────────────────────────────────────────────────┤
│ 3. Coordination Layer (orchestrator: poll/claim)   │
├─────────────────────────────────────────────────────┤
│ 4. Execution Layer (workspace + Codex subprocess)    │
├─────────────────────────────────────────────────────┤
│ 5. Integration Layer (Linear API adapter)          │
├─────────────────────────────────────────────────────┤
│ 6. Observability Layer (structured logs + optional UI)│
└─────────────────────────────────────────────────────┘
```

### Core Components

| Component | Responsibility |
|-----------|---------------|
| **Workflow Loader** | Reads `WORKFLOW.md`, parses YAML front matter + prompt body |
| **Config Layer** | Typed getters; applies defaults, env var indirection, validation |
| **Issue Tracker Client** | Fetches candidate/terminal issues from Linear; normalizes to stable issue model |
| **Orchestrator** | Owns poll tick, in-memory state, eligibility decisions, retry queue |
| **Workspace Manager** | Maps issue IDs → workspace paths; runs lifecycle hooks; cleans terminal workspaces |
| **Agent Runner** | Creates workspace, builds prompt, launches Codex app-server client, streams events |
| **Status Surface** (opt) | Phoenix LiveView dashboard, JSON API at `/api/v1/*` |

### Key Design Decisions

- **No persistent DB required**: Tracker/filesystem-driven restart recovery. In-memory scheduler state is NOT restored on restart — only workspace + tracker state survive.
- **WORKFLOW.md as contract**: Workflow policy lives in the repo, versioned with code. Teams own the agent prompt.
- **Per-issue isolation**: Each issue gets a dedicated workspace directory; agent commands run only inside it.
- **Bounded concurrency**: `max_concurrent_agents` limits parallel Codex sessions.
- **Deterministic workspaces**: Workspace key = sanitized `issue.identifier` (alphanumeric + `._-` only).
- **Linear `linear_graphql` tool**: During Codex sessions, Symphony serves a client-side `linear_graphql` tool so the agent can read/write its own ticket.

## Domain Model

| Entity | Description |
|--------|-------------|
| `Issue` | Normalized record: `id`, `identifier` (e.g. `ABC-123`), `title`, `state`, `priority`, `labels`, `blocked_by`, timestamps |
| `WorkflowDefinition` | Parsed `WORKFLOW.md`: `{config, prompt_template}` |
| `Workspace` | Per-issue filesystem workspace: `{path, workspace_key, created_now}` |
| `RunAttempt` | One execution attempt per issue: `{issue_id, attempt, workspace_path, started_at, status, error}` |
| `LiveSession` | Codex subprocess metadata: `session_id`, token counts, turn count, last event |
| `RetryEntry` | Scheduled retry: `{issue_id, attempt, due_at_ms, error}` |
| `OrchestratorRuntimeState` | In-memory authoritative state: `running`, `claimed`, `retry_attempts`, `completed`, rate limits |

## Elixir/OTP Reference Implementation

The `elixir/` directory is a **prototype**, not hardened production code. Built on BEAM/OTP with Restate for durable state (see `ACNoonan/symphony-restate` for a Restate-based fork with full durability).

### Project Layout

```
elixir/
  lib/              — application code + Mix tasks
  test/             — ExUnit coverage
  WORKFLOW.md        — in-repo workflow contract template
  ../.codex/         — repo-local Codex skills (linear, commit, push, pull, land)
```

### Run Locally

```bash
git clone https://github.com/openai/symphony
cd symphony/elixir
mise trust && mise install
mise exec -- mix setup
mise exec -- mix build
mise exec -- ./bin/symphony ./WORKFLOW.md
```

Optional flags:
- `--logs-root` — log directory (default: `./log`)
- `--port` — enables Phoenix LiveView dashboard + JSON API

### WORKFLOW.md Example

```md
---
tracker:
  kind: linear
  project_slug: "my-project"
workspace:
  root: ~/code/workspaces
hooks:
  after_create: |
    git clone git@github.com:your-org/your-repo.git .
agent:
  max_concurrent_agents: 10
  max_turns: 20
codex:
  command: codex app-server
---

You are working on a Linear issue {{ issue.identifier }}.
Title: {{ issue.title }}
Body: {{ issue.description }}
```

## Key Files

```
raw/refs/symphony/
  SPEC.md              — Full language-agnostic specification (normative)
  README.md            — Top-level README
  elixir/README.md     — Elixir reference implementation guide
  elixir/WORKFLOW.md  — Workflow template used by the reference impl
  .github/media/       — Demo video + screenshots
```

## Ecosystem / Forks

| Repo | Description |
|------|-------------|
| [ACNoonan/symphony-restate](https://github.com/ACNoonan/symphony-restate) | Symphony on Restate — durable execution layer replaces in-memory orchestrator; every issue maps to a Restate Virtual Object for claim durability + cross-node failover |
| [jvogan/symphony-claude-lane](https://github.com/jvogan/symphony-claude-lane) | Portable skill adding a Claude Code lane to Symphony + Linear workflows |

## vs Similar Tools

| Dimension | Symphony | ruflo |
|-----------|----------|-------|
| **Origin** | OpenAI | RuvNet |
| **Core model** | Linear → isolated Codex sessions | Claude Code extension |
| **Multi-agent topology** | Per-issue isolated (no swarm) | 100+ agents, mesh/hierarchical swarm |
| **Memory** | Workspace-scoped, no persistent DB | AgentDB + HNSW + SONA |
| **Language** | Elixir/OTP spec + reference impl | TypeScript/Node.js |
| **Trust model** | Trusted environment preview | Enterprise zero-trust federation |
| **Primary use** | Automated sprint work | Full development lifecycle orchestration |

## Links

- [Homepage](https://openai.com/index/open-source-codex-orchestration-symphony/)
- [SPEC.md](https://github.com/openai/symphony/blob/main/SPEC.md)
- [Elixir README](https://github.com/openai/symphony/blob/main/elixir/README.md)
- [Codex App Server](https://developers.openai.com/codex/app-server/)
- [Harness Engineering](https://openai.com/index/harness-engineering/)
