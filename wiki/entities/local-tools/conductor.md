---
title: Conductor
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/glundgren93/conductor
tags:
  - local-tools
  - agent orchestration
  - AI engineering
---

# Conductor

**Agent orchestration platform** — plan, build, operate.

## Features

Conductor runs the real software engineering lifecycle with AI agents:

- **Planner**: Analyzes the task
- **Builder**: Implements the solution
- **Operator**: Tries to break it adversarially
- **Cycle repeats** until the result is solid

You supervise; agents execute.

## Usage

```bash
# Run in background (default) — returns immediately with job ID
conductor run --task "add caching to /users endpoint" --project ~/git/my-app

# Check status of a background job
conductor status <job-id>

# Run in foreground (blocks until done)
conductor run --task "fix bug in auth module" --project . --foreground

# With budget cap ($2 max)
conductor run --task "add pagination" --project . --budget 2.00

# Fully autonomous, auto-open PR
conductor run --task "add pagination" --project . --auto --pr
```

## Architecture

The Build→Operate→Fix cycle with adversarial testing ensures robust outputs through iterative refinement.

## Installation

```bash
npm install
npm run build
```
