---
title: hermes-skill-factory
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/romanescu11-hermes-skill-factory.md
tags:
  - skill
  - open-source
  - session-extraction
confidence: high
---
# hermes-skill-factory（Romanescu11）

> **511 ⭐ / Python**。Hermes Agent 的 **meta-skill 插件**：观察你重复做的工作流，自动产出可复用的 Hermes skill。

## 定位

解决「**创建 skill 的门槛太高**」这个具体痛点。你不需要自己写 SKILL.md 的 frontmatter、步骤、pitfalls——指出一个你重复做的流程，它生成。

## 与本 wiki 所在实例的关系

Hermes 原生已有 `skill_manage` 工具和「complex task 后提示存 skill」的 nudge 机制（见 [[hermes-skill-nudge]]）。hermes-skill-factory 是把这一步**从提示升级为自动生成**。

| 层次 | 实现 |
|------|------|
| 提示你存 skill | Hermes 原生 nudge（见 [[hermes-skill-nudge]]）|
| **自动生成 skill** | **hermes-skill-factory** |
| 自动改进已有 skill | [[SkillClaw]] |
| 演化 prompt+skill+代码 | [[hermes-agent-self-evolution]] |

四者构成 Hermes skill 生命周期的完整覆盖。

## 相关页面

- [[hermes-skill-nudge]] — Hermes 原生的 skill 建议机制
- [[SkillClaw]] / [[hermes-agent-self-evolution]] — 同生态
- [[Skill-Distillation-Research]] — 学术层
