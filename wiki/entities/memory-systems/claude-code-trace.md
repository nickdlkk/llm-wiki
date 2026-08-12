---
title: claude-code-trace
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/delexw-claude-code-trace.md
tags:
  - session-extraction
  - open-source
confidence: high
---
# claude-code-trace（delexw）

> **345 ⭐ / Rust**。Claude Code 的 session log viewer，读 `~/.claude/projects` 下的 JSONL。GUI（macOS/Linux/Windows）+ Web + TUI 三形态。

## 定位

**纯读侧工具**——不提取、不改写，只让 session 可读可搜。是 session-mining 的**前置基础设施**：先能看清 session 里有什么，才能设计提取规则。

## 能力

- 实时 tail
- 跨 session 搜索
- MCP tool 调用识别

## 为什么收录

做任何 session miner 之前，都需要先理解目标 JSONL 的实际结构。claude-code-trace 的 Rust 解析器是一份可参考的实现，且三形态（GUI/Web/TUI）覆盖了不同调试场景。

## 相关页面

- [[obelisk]] — 同为可视化但含 subagent
- [[Session-Extraction-Pipeline]] — 范式总览（Step 1 采集）
