---
title: orbit
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/krzemienski-orbit.md
tags:
  - memory
  - session-extraction
  - open-source
  - drift-detection
confidence: high
---

# orbit（krzemienski）

> **0 ⭐ / HTML / MIT / 2026-05-05 push**。Native Claude Code 插件，**「Find drift. Close gaps. Restore alignment.」**——做 intent mining、session-history gap analysis、claim validation、codebase truth checks、dashboard 可视化。

## 定位

**「漂移检测 + 缺口分析 + 对齐还原」**——把 session history 看作连续体，自动发现：

- **drift（漂移）**：agent 行为与初始 intent 的偏离
- **gap（缺口）**：session history 中缺失的关键信息
- **alignment（对齐）**：当前代码库与之前 session 决策的一致性

## 关键能力

| 能力 | 说明 |
|------|------|
| **intent mining** | 从 session history 挖出真实意图（不只是字面任务）|
| **session-history gap analysis** | 找出 session 之间的连续性断点 |
| **claim validation** | 验证 session 里做出的声明 vs 实际代码 |
| **codebase truth checks** | session 决策 vs 仓库当前状态 |
| **dashboard** | 可视化 |

## 关键设计：「drift / gap / alignment」三件套

| 概念 | 含义 | 类比 |
|------|------|------|
| **drift** | agent 偏离了用户最初意图 | 软件项目里的「scope creep」|
| **gap** | session 之间缺什么 | commit 之间的冲突未解 |
| **alignment** | 代码 vs 决策的一致性 | 文档 vs 实现的漂移 |

**这是「复盘」概念的更深一层**——不只是「这次 session 学到什么」（[[reflect-skill-claude]]），而是「agent 整体行为是否还在正轨上」。

## 与本 wiki 已有项目的关系

| 项目 | 关注点 |
|------|--------|
| [[orbit]] | 跨 session 的 drift / gap / alignment |
| [[reflect-skill-claude]] | 单 session 内的 3 阶段复盘 |
| [[agent-usage-analyze]] | 跨 session 的「改进追踪」|
| [[Open-Amnesia]] | 多源 session → memory |

**orbit 关注的是「行为一致性」**，而 [[reflect-skill-claude]] 关注「单次经验」，二者互补。

## 调研反思

0 ⭐ + 名字（orbit）太通用的双重原因让前两轮 sort=stars 搜索完全错过。补搜时通过 "intent mining" 关键词才浮现。

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式总览
- [[reflect-skill-claude]] — 单次复盘
- [[agent-usage-analyze]] — 跨 session 改进追踪
- [[Skill-Distillation-Research]] — 学术层
