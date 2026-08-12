---
title: agent-memory-engine
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - https://github.com/uudam42/agent-memory-engine
  - raw/articles/uudam42-agent-memory-engine.md
tags:
  - memory
  - mcp-server
  - local-first
  - coding-agent
  - memory-engine
---

# agent-memory-engine (uudam42)

**agent-memory-engine** 是 [uudam42](https://github.com/uudam42) 的 **local-first MCP memory server**（123 ⭐，2026-08）。给 coding agent 提供**持久化、跨 session 的 memory + project knowledge** 运行时。

> ⚠️ **名称注意**：本项目 `uudam42/agent-memory-engine` **不要**与已有 [[agentmemory]] (jayzeng/npm: `myagentmemory`) 混淆——
> - **agent-memory-engine** = MCP server 形态，本地运行时，Python
> - **agentmemory** = npm CLI + Markdown 文件，TS/JS

## 核心能力

- **Persistent memory**：跨 session
- **Project knowledge**：项目级上下文
- **MCP server**：标准 MCP 协议给 coding agent 用
- **Local-first**：完全本地
- **多 coding agent**：通过 MCP 协议通用

## 与本调研的契合点

| 调研需求 | 契合度 |
|---------|--------|
| "扫描历史会话" | ❌ **不直接做 ingest**（这是它下游的 [[Memory-Systems]] 同类产品） |
| "持久 memory" | ✅ 核心功能 |
| "MCP 集成" | ✅ |
| "本地" | ✅ |

> 它是**下游**——给 agent 提供 memory 运行时；会话扫描应该用 [[Open-Amnesia|Open Amnesia]]、[[cc-analyst]]、[[reflect-skill-claude]]。

## 相关页面

- [[agentmemory]] — 名称相似但是不同项目
- [[Open-Amnesia|Open Amnesia]] — 上游扫描
- [[cc-analyst]] — 上游扫描
