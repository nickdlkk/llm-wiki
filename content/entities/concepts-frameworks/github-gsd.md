---
title: Get Shit Done (GSD)
type: entity
source: github:gsd-build/get-shit-done
created: 2026-04-17
updated: 2026-04-17
tags:
  - sdd
  - meta-prompting
  - context-hygiene
  - claude-code
  - opencode
---

# Get Shit Done (GSD)

*"Context hygiene for AI coding agents."*

**Repo:** `gsd-build/get-shit-done` | **Lang:** TypeScript | **License:** MIT | **Status:** Active

---

## Summary

GSD is a lightweight spec-driven development framework with a unique focus on **context rot prevention** — the quality degradation that happens as Claude fills its context window. It combines the BMAD Method, SDD conventions, and session management into a pragmatic daily-driver tool.

Claimed to be "the most practical SDD tool in daily use" by its maintainers.

---

## Core Philosophy

**Context rot is the enemy.** Unlike OpenSpec (which focuses on spec delta format) or Spec-Kit (which focuses on workflow programming), GSD treats *context management* as the primary engineering challenge.

**Three layers of GSD:**
1. **Meta-prompting** — How to frame requests to AI coding agents
2. **Session management** — When and how to refresh context
3. **Skill system** — Reusable agent configurations

---

## Supported Tools

Claude Code, OpenCode, Gemini CLI, Kilo, Codex, Copilot, Cursor, Windsurf, Antigravity, Augment, Trae, Qwen Code, Cline, CodeBuddy.

---

## Key Features

- **BMAD Method** — Breakdown, Method, Approach, Do (structured problem decomposition)
- **Context hygiene rules** — When to summarize, when to refresh, when to start fresh
- **Skill system** — Bundled skills as reusable agent configurations
- **Session state management** — Tracks what has been done/decided across long sessions
- **Multi-language** — Full i18n (Japanese, Korean, Portuguese, Chinese, English)

---

## Strengths

- **Lowest ceremony** of all SDD tools — minimal setup, works immediately
- **Trust network** — used by engineers at Amazon, Google, Shopify, Webflow
- **Context hygiene focus** is unique — addresses the actual pain of long coding sessions
- **Supports 13+ AI tools** — most universal tool support of any SDD framework

## Weaknesses

- Less formal spec format than OpenSpec — more convention than structure
- No explicit artifact delta tracking
- Opinionated about session management workflow
