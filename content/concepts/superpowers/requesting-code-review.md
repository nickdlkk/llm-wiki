---
title: Requesting Code Review
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Requesting Code Review

> Source: skills/requesting-code-review/SKILL.md

## 核心工作流

**核心原则：** 早审查、勤审查。

### 发起代码审查的流程

```bash
# 1. 获取 git SHAs
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)

# 2. 派发 code-reviewer subagent
Task(superpowers:code-reviewer, {
  WHAT_WAS_IMPLEMENTED: "...",
  PLAN_OR_REQUIREMENTS: "...",
  BASE_SHA: "...",
  HEAD_SHA: "...",
  DESCRIPTION: "..."
})

# 3. 根据反馈行动
```

### 严重性分级

| 级别 | 含义 | 处理策略 |
|------|------|----------|
| **Critical** | 阻塞性问题 | 立即修复，不允许绕过 |
| **High** | 重要问题 | 继续前必须修复 |
| **Medium** | 中等问题 | 记录，酌情修复 |
| **Low** | 轻微问题 | 后续处理 |
| **INFO** | 信息类 | 可忽略或后续考虑 |

### Critical 问题阻塞机制

```
┌─────────────────────────────────────────────┐
│  Critical Issues Found                      │
│  ───────────────────────                    │
│  □ 安全性漏洞                                │
│  □ 功能性破坏                                │
│  □ 数据丢失风险                              │
│                                             │
│  → BLOCK: 必须全部修复后才能继续              │
│  → 不允许标注 "后续处理"                      │
└─────────────────────────────────────────────┘
```

## 何时请求审查

**强制审查：**
- 子 Agent 驱动开发中的每个 task 完成后
- 重大功能完成后
- 合并到 main 前

**可选但有价值：**
- 卡住时（获得新视角）
- 重构前（基线检查）
- 修复复杂 bug 后

## 与其他技能的关系

- **dispatching-parallel-agents**: 多个独立问题可并行调查后再统一审查
- **receiving-code-review**: 接收审查反馈时遵循 6 步响应模式
- **Subagent-Driven Development**: 每个 task 后必须审查，防止问题累积
- **Executing Plans**: 每 3 个 task 批次审查一次

## Red Flags

| 禁止行为 | 说明 |
|----------|------|
| "这很简单，跳过审查" | 所有代码都需要审查 |
| 忽略 Critical 问题 | 必须立即修复 |
| 有未修复的 High 问题继续 | 必须在继续前修复 |
| 与有效的技术反馈争论 | 专注技术，而非防御 |

**如果 reviewer 有误：** 提供技术推理证明，请求澄清。

