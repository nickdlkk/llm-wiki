---
title: agent-retrospective
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/weak-fox-agent-retrospective.md
tags:
  - memory
  - session-extraction
  - open-source
  - retrospective
confidence: high
---

# agent-retrospective（weak-fox）

> **1 ⭐ / Python / 2026-05-27 push**。Local-first **Codex skill + CLI** 做增量 agent session 复盘、反思报告、多 agent session 分析。

## 定位

仓库名就是它的全部定位：**retrospective**（复盘）。与本 wiki 已收录项目的差异在于「**增量**」——不是一次性分析整个 session，而是**逐步累积**反思报告。

## 关键能力（README 摘要）

- **Local-first** —— 数据全在本地
- **Codex skill** —— 通过 Codex 的 skill 机制触发
- **CLI** —— 也可手动跑
- **Incremental** —— 增量式复盘（不是一次性 bulk 分析）
- **Reflection reports** —— 反思报告
- **Multi-agent session analysis** —— 覆盖 subagent session

## 与本 wiki 已有项目的关系

| 项目 | 复盘能力 | 增量 |
|------|---------|------|
| [[reflect-skill-claude]] | ✅ 3 阶段 | ❌ 一次性 |
| [[agent-retrospective]] | ✅ 持续 | ✅ 增量 |
| [[agent-usage-analyze]] | ✅ 改进追踪 | ✅ 持续 |

「增量复盘」是这几家共有的设计意图，但只有 weak-fox 和 zm2529 明确以 Codex 为主要集成对象。

## 调研反思

这个项目也是 1 ⭐，在 sort=stars 排序下被 90k+ 大仓库淹没。**与 [[agent-usage-analyze]] 同一类盲区**——低 star + 中文/英文 README 关键词不典型。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[reflect-skill-claude]] — 同一复盘意图
- [[agent-usage-analyze]] — 同一增量复盘意图
- [[Skill-Distillation-Research]] — 学术层
