---
title: writing-skills
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

# writing-skills

> Source: `skills/writing-skills/SKILL.md`

**写技能 = 测试驱动开发应用于流程文档。**

核心原则：若未观察 agent 在无技能时的失败，就不知道技能能否教会正确行为。

## TDD 映射

| TDD 概念 | 技能创建 |
|---------|---------|
| Test case | subagent 压力测试场景 |
| Production code | SKILL.md 文档 |
| Test fails (RED) | Agent 无技能时违反规则 |
| Test passes (GREEN) | Agent 有技能时遵守规则 |
| Refactor | 封闭漏洞同时保持合规 |

## 核心铁律：No Skill Without a Failing Test First

新技能 + 技能修改都必须先运行测试。无例外：
- 不适用于"简单添加"
- 不适用于"文档更新"
- 不适用于"先写后测"

## SKILL.md 结构

```yaml
---
name: Skill-Name-With-Hyphens
description: Use when [specific triggering conditions and symptoms]
---

# Skill Name

## Overview
## When to Use
## Core Pattern
## Quick Reference
## Common Mistakes
```

## YAML Frontmatter 规范

| 字段 | 规范 |
|------|------|
| `name` | 仅字母、数字、连字符（无括号/特殊字符） |
| `description` | 第三人称，以 "Use when..." 开头，**仅描述触发条件**，不描述工作流程，<500 字符 |
| 总长度 | ≤1024 字符 |

**description 陷阱：** 描述 workflow 摘要会导致 Claude 跳过正文只读描述。应只写触发条件。

## Claude Search Optimization (CSO)

技能可被发现的关键：
1. **description 写触发条件**，不用 "Use when..." 以外的句式
2. **关键词覆盖**：错误信息、症状、同义词、工具名
3. **命名用动词**：creating-skills > skill-creation

## 技能类型

| 类型 | 测试方式 |
|------|---------|
| Discipline（纪律约束型，如 TDD） | 压力场景 + 反合理化表 |
| Technique（技术方法型） | 应用场景测试 |
| Pattern（思维模式型） | 识别 + 应用测试 |
| Reference（参考文档型） | 检索准确性测试 |

## 抗合理化机制

| 借口 | 现实 |
|------|------|
| "太明显不需要测试" | 对你明显 ≠ 对其他 agent 明显 |
| "测试是过度工程" | 无测试技能必然有问题 |
| "我确信它是对的" | 过度自信 = 必然出问题 |
| "没有时间测试" | 部署后调试浪费更多时间 |

**关闭漏洞三步：** 识别合理化 → 添加显式反例 → 重测

## 集成

- REQUIRED BACKGROUND：`superpowers:test-driven-development`（必须先理解 RED-GREEN-REFACTOR）
- RED phase 前必须读 `testing-skills-with-subagents.md`
