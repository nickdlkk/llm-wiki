---
title: MemSkill
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - raw/articles/viktoraxelsen-memskill.md
tags:
  - memory
  - skill
  - open-source
confidence: high
---
# MemSkill（ViktorAxelsen）

> **560 ⭐ / Python / Apache-2.0**。论文《MemSkill: Learning and Evolving **Memory Skills** for Self-Evolving Agents》的实现。

## 核心概念：Memory Skill

把「**如何使用记忆**」本身当成一种可学习、可演化的 skill——不只是存和取，而是学会「什么时候该回忆什么」。

## 在谱系中的位置

| 层次 | 代表 |
|------|------|
| 记忆内容演化 | [[Agentic-Context-Engine-ACE]]（playbook delta）|
| **记忆使用策略演化** | **MemSkill** / [[MemRL]] |
| 记忆基础设施演化 | EvolveMem |

三层递进：先演化「记什么」，再演化「怎么用」，最后演化「用什么系统记」。

## 相关页面

- [[MemRL]] — 同类（RL 框架）
- [[Skill-Distillation-Research]] — 学术层总览
- [[Session-Extraction-Pipeline]] — 工程范式
