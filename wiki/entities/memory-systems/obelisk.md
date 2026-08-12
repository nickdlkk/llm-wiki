---
title: obelisk
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/tommy0103-obelisk.md
tags:
  - memory
  - session-extraction
  - open-source
confidence: high
---
# obelisk（tommy0103）

> **314 ⭐ / JavaScript / AGPL-3.0**。"Every past session, subagent, and workflow — queryable by your agent, browsable by you."

## 定位

**双消费者设计**：同一份 session 数据，既给 agent 查（程序化），又给人浏览（UI）。而且**显式覆盖 subagent 和 workflow**——多数工具只处理主会话。

## 为什么 subagent 覆盖重要

本 wiki 所在实例大量使用 `delegate_task` 派生 subagent。subagent 的完整推理过程默认只回传一个 summary，**中间过程会丢失**。obelisk 这类工具把 subagent 轨迹也纳入可查范围，直接对应这个盲区。

## 注意

**AGPL-3.0** — 如果打算改造后作为服务提供，需注意 copyleft 传染性。这与本 wiki 收录的多数 MIT/Apache 项目不同。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[claude-code-trace]] — 同为 session 可视化
- [[code-session-memory]] — 跨 harness 检索
