---
title: obsidian-second-brain
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/eugeniughelbur-obsidian-second-brain.md
tags:
  - memory
  - wiki
  - open-source
  - session-extraction
confidence: high
---
# obsidian-second-brain（eugeniughelbur）

> **3,963 ⭐ / Python / MIT**。为 **Claude Code + 另外 6 个 CLI agent** 提供持久记忆，存成 Obsidian vault 里的纯 Markdown。

## 定位

**Obsidian 路线里 star 数最高的**（3,963 ⭐ vs [[open-second-brain]] 的 153 ⭐），但**不以 Hermes 为主要集成对象**。

| 项目 | Stars | Hermes 支持 | 核心差异 |
|------|-------|------------|---------|
| **obsidian-second-brain** | 3,963 | 泛 CLI 之一 | 覆盖广、生态成熟 |
| [[open-second-brain]] | 153 | **primary** | dream pass 确定性晋升、Hermes 掌管调度 |

对 Nick 而言：**要生态成熟度选前者，要 Hermes 深度集成选后者**。

## 共同的架构选择

两者都选了「**纯 Markdown + Obsidian vault**」而非向量库，理由一致：
- 数据归用户，可肉眼审查
- Obsidian 的 wikilink / backlink / graph view 免费复用
- 无 schema 迁移风险

这与本 wiki（[[llm-wiki]] skill，纯 Markdown + Obsidian 兼容）是同一哲学。

## 相关页面

- [[open-second-brain]] — Hermes-first 的对照
- [[llm-wiki]] — 本 wiki 的 skill
- [[Session-Extraction-Pipeline]] — 范式总览
