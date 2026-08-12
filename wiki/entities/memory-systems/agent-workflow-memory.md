---
title: agent-workflow-memory
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/zorazrw-agent-workflow-memory.md
tags:
  - skill
  - memory
  - open-source
  - benchmark
confidence: high
---
# agent-workflow-memory (AWM)

> **456 ⭐ / Python / Apache-2.0 / ICML 2025 / arXiv [2409.07429](https://arxiv.org/abs/2409.07429)**。从过往任务轨迹归纳可复用 **workflow**。

## 核心思想

轨迹 → **list-of-steps 形式的 workflow**。不是提取"事实"，也不是生成可执行代码，而是中间形态：**步骤序列**。

这个粒度选择很关键：
- 比 facts 更可操作（facts 只是知识）
- 比 code skill 更鲁棒（代码会因环境变化失效）
- 正好对应 Claude Code / Hermes 的 **SKILL.md「numbered steps」**格式

## 为什么与本 wiki 直接相关

本 wiki 所在实例的 skill 格式就是「trigger + 编号步骤 + pitfalls」。AWM 是这个格式的**学术依据**——它论证了 list-of-steps 是从轨迹归纳的最优粒度。

工程对应实现见 [[welshe-traceforge]]（capture-distill-inject 三段）。

## 相关页面

- [[Skill-Distillation-Research]] — 学术层总览
- [[welshe-traceforge]] — 工程实现
- [[Session-Extraction-Pipeline]] — 范式总览
