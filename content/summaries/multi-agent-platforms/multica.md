---
title: "Multica — Open-Source Managed Agents Platform"
date: 2026-05-09
source: https://github.com/multica-ai/multica
stars: 26275
forks: 3178
---

# Multica — Summary

**Multica** is an open-source platform (26K GitHub stars, Jan 2026) that manages AI coding agents as first-class team members — assigning them issues, tracking progress, and compounding reusable skills across the team.

## What It Does

Instead of copy-pasting prompts into a CLI, you assign issues to an agent from a task board. The agent autonomously picks up work, executes it on the runtime, reports blockers, and closes issues. Multiple humans and multiple agents share the same workspace.

**Supported agents:** Claude Code, Codex, Copilot CLI, OpenClaw, OpenCode, Hermes, Gemini, Pi, Cursor Agent, Kimi, Kiro CLI.

## Architecture

- **Frontend:** Next.js 16 → served as Docker container
- **Backend:** Go (Chi + gorilla/websocket) → single binary
- **Database:** PostgreSQL 17 + pgvector
- **Agent execution:** Local daemon on each team member's machine (not server-side)

The daemon auto-detects installed agent CLIs and registers them as runtimes. Tasks are polled (default 3s) and executed in isolated workspace directories with WebSocket streaming back to the server.

## Self-Hosting

Two commands to deploy full stack (backend + frontend + Postgres) via Docker Compose:

```bash
curl -fsSL .../install.sh | bash -s -- --with-server
multica setup self-host
```

Images from GHCR (`multica-backend:latest`, `multica-web:latest`). Login via email code (Resend) or backend log. Pin `MULTICA_IMAGE_TAG` in `.env` for version control. Migrations run automatically on backend startup.

## Key Insight

The name nods to Multics (1960s time-sharing OS). Just as Multics let multiple users share one machine, Multica lets multiple humans + agents share one project — multiplexing the team.

## Competitive Position

- **vs Paperclip:** Cloud-first multi-user (Multica) vs local-first single-user (Paperclip)
- **vs Slock.ai:** Self-hostable open source (Multica) vs SaaS-only (Slock)
- **vs Chorus:** Full lifecycle management (Multica) vs lightweight task sync (Chorus)
