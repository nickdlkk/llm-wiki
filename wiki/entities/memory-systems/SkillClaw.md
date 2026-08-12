---
title: SkillClaw
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/amap-ml-skillclaw.md
tags:
  - skill
  - open-source
  - session-extraction
  - optimization
confidence: high
---
# SkillClaw（AMAP-ML）

> **2,413 ⭐ / Python / 阿里高德 AMAP-ML 出品**。"Let Skills Evolve Collectively with Agentic Evolver"——**直接对 `~/.hermes/skills/` 做 post-task 进化循环**的 Hermes 原生插件。

## 定位

不是记忆层，而是 **skill library 的自动维护者**：每次任务后跑 evolution loop，对现有 skill 做去重、改进、合并。

| 维度 | 值 |
|------|-----|
| Stars | 2,413 ⭐ |
| 出品方 | AMAP-ML（高德地图机器学习团队）|
| 目标 | Hermes Agent skills 目录 |
| 机制 | post-task agentic evolver |

## 安全设计（值得借鉴）

带专门的运维子命令：
- `skillclaw doctor hermes` — 体检
- `skillclaw restore hermes` — 回滚

**任何自动改写 skill 的工具都必须有 restore**——这与 [[cc-analyst]] 的 backup/rollback、[[Session-Extraction-Pipeline]] 的「Rollback by default」原则一致。

## 与 Hermes 生态其他方案的分工

| 项目 | 改什么 | 谁维护 |
|------|--------|--------|
| [[hermes-agent-self-evolution]] | prompt / skill / tool desc / 代码，出 PR | **官方** |
| **SkillClaw** | `~/.hermes/skills/` 去重改进 | 社区（AMAP-ML）|
| [[hermes-skill-factory]] | 生成**新** skill | 社区 |
| [[open-second-brain]] | Obsidian vault 记忆层 | 社区 |

## 注意

自动改写 skill 目录有风险：本 wiki 所在实例的 skills 有大量手工维护内容（含 Nick 的个人约定）。启用前务必先跑 `doctor`，并确认 restore 可用。

## 相关页面

- [[hermes-agent-self-evolution]] — 官方路线
- [[Skill-Distillation-Research]] — Voyager skill library 谱系
- [[Session-Extraction-Pipeline]] — 范式总览
