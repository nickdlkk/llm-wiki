---
title: llmwiki (lucasastorian)
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/lucasastorian-llmwiki.md
tags:
  - wiki
  - ingest
  - open-source
  - knowledge-base
confidence: high
---
# llmwiki（lucasastorian）

> **1,491 ⭐ / Python**。Karpathy LLM Wiki 的**开源工程级实现**：MCP server 形态，可接 Slack / Granola / Notion 等所有源，由 Claude 自动 ingest 并维护 wiki。

## 与本 wiki 的关系

本 wiki 用的是 [[llm-wiki]] **skill**（agent-in-the-loop，人工投喂源）。lucasastorian/llmwiki 是同一 Karpathy 架构的 **MCP server** 实现，差别在**采集面**：

| 维度 | 本 wiki 的 llm-wiki skill | lucasastorian/llmwiki | [[claude-memory-compiler]] |
|------|--------------------------|----------------------|---------------------------|
| 形态 | Hermes skill | MCP server | Claude Code hooks |
| 原料 | 人工给 URL / 文章 | **Slack / Granola / Notion 等连接器** | **自己的 session transcript** |
| 自动化 | 人在环 | 半自动 | **全自动** |
| 检索 | index.md 优先 | index 引导 | index 引导（无 RAG）|

三者共享同一个核心信念：**个人规模下，结构化 index 优于向量检索**。

## 值得借鉴

**连接器思路**——把会议纪要（Granola）、团队沟通（Slack）也当 wiki 原料。本 wiki 目前只吃文章和 GitHub README，而 Nick 的飞书会话、Notion 页面同样是高价值原料。

## 相关页面

- [[llm-wiki]] — 本 wiki 使用的 skill
- [[claude-memory-compiler]] — session → Karpathy wiki 的自动化版
- [[Session-Extraction-Pipeline]] — 范式总览
