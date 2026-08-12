---
title: code-session-memory
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/djannot-code-session-memory.md
tags:
  - memory
  - session-extraction
  - open-source
  - ingest
confidence: high
---
# code-session-memory（djannot）

> **18 ⭐ / TypeScript**。**跨 6 个 harness 的统一 session 向量记忆**：OpenCode / Claude Code / Cursor / VS Code / Codex / Gemini CLI 的 session 存进**同一个**数据库。

## 为什么值得收录

本调研中**多 harness 覆盖最广**的 session miner，且架构与 Hermes 的 SQLite FTS5 高度同构——可作为 Hermes 做跨 agent 记忆的参考实现。

## 机制

每次 AI agent 结束一个 turn，自动把**新增消息**索引进向量库。过去的 session 变成语义可搜——既能被 agent 搜（通过 MCP server），也能被你自己搜。

**只索引新消息**（通过 `sessions_meta` 表追踪），已索引的跳过。所以即使 session 很长，每次索引也很快——这是**增量幂等**的标准做法。

## 存储选项

| 后端 | 场景 |
|------|------|
| **sqlite-vec**（默认）| 本地单机 |
| **PostgreSQL + pgvector**（如 Supabase）| 多机共享记忆 |

Embedding 用 OpenAI `text-embedding-3-large`。

## 安装（一条命令搞定所有已检测到的工具）

```bash
npx code-session-memory install
```

以 Claude Code 为例，它会：
1. 往 `~/.claude/settings.json` 写一个 `Stop` hook（每个 agent turn 后触发）
2. 把 skill 拷到 `~/.claude/skills/code-session-memory/SKILL.md`
3. 往 `~/.claude.json` 写 MCP server 条目

**hook（写入）+ skill（教 agent 怎么用）+ MCP（读取）三件套**是当前 session-miner 的标准安装形态。

## 与本 wiki 已收录项目对比

| 项目 | 覆盖 harness 数 | 提取 skill? |
|------|----------------|-------------|
| **code-session-memory** | **6** | ❌ 只做语义检索 |
| [[Open-Amnesia]] | 3（含 iMessage）| ✅ facts + skills |
| [[cc-analyst]] | 2 | ✅ |
| [[claude-mem-thedotmack]] | 2 | ❌ |

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[cass-memory]] — 另一条跨 agent 路线
- [[Open-Amnesia]] — 多源采集 + 提取
