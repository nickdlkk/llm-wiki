---
title: using-superpowers
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
  - meta
---

# using-superpowers

> Source: `skills/using-superpowers/SKILL.md`

Superpowers 技能系统的使用规范和优先级规则。

## 核心规则

**任何响应或操作前调用相关技能。** 即使有 1% 的可能性技能适用，也必须调用技能检查。

> IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE. YOU MUST USE IT.

## 指令优先级

```
1. 用户显式指令（CLAUDE.md / GEMINI.md / AGENTS.md / 直接请求）— 最高
2. Superpowers skills — 覆盖默认系统行为
3. 默认系统 prompt — 最低
```

**冲突时：** 用户指令 > Superpowers skills。

## 技能发现流程

```
收到用户消息 → 是否有任何技能可能适用？
  ├─ 是 → 加载技能 → 宣布 "I'm using [skill] to [purpose]"
  │         ├─ 有 checklist → 为每项创建 TodoWrite
  │         └─ 无 → 直接遵循
  └─ 否 → 直接响应
```

## Red Flags（停止信号 = 你在合理化）

| 想法 | 现实 |
|------|------|
| "这只是简单问题" | 问题即任务，先检查技能 |
| "我需要先获取上下文" | 技能检查在澄清问题之前 |
| "让我先探索代码库" | 技能告诉你如何探索，先检查 |
| "这不需要正式技能" | 技能存在就用 |
| "这个技能是杀鸡用牛刀" | 简单的事会变复杂，用它 |
| "我知道那是什么意思" | 知道概念 ≠ 使用技能 |

## 技能分类处理

| 类型 | 处理方式 |
|------|---------|
| **Rigid（严格型）** | 严格遵循，不适应（如 TDD） |
| **Flexible（灵活型）** | 根据上下文调整原则 |

技能本身会说明是哪种类型。

## 跨平台工具映射

| 平台 | 技能工具 |
|------|---------|
| Claude Code | `Skill` tool |
| GitHub Copilot CLI | `skill` tool |
| Gemini CLI | `activate_skill` tool |
| 其他 | 参见平台文档 |
