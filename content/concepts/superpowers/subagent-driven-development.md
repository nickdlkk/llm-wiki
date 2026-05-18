---
title: Subagent-Driven Development
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Subagent-Driven Development

> Source: skills/subagent-driven-development/SKILL.md

## 核心工作流

**7步主流程：**

```
┌─────────────────────────────────────────────────────────────┐
│  1. Read plan → Extract all tasks → Create TodoWrite       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  Per Task Loop:                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  2. Dispatch implementer subagent                   │   │
│  │  3. Spec compliance review (spec-reviewer prompt)   │   │
│  │  4. Code quality review (code-quality-reviewer)     │   │
│  │  5. Fix loop if issues found → re-review           │   │
│  │  6. Mark task complete in TodoWrite                 │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  7. Dispatch final code reviewer → finishing-a-branch      │
└─────────────────────────────────────────────────────────────┘
```

**模型选择策略：**
| 任务类型 | 模型选择 |
|----------|----------|
| 机械实现（1-2文件，清晰规格） | 快速便宜模型 |
| 集成与判断（多文件协调、调试） | 标准模型 |
| 架构、设计、审查 | 最强模型 |

## 铁律 / HARD-GATE

**两阶段审查顺序不可颠倒：**
1. **Spec Compliance Review** (第一优先级)
2. **Code Quality Review** (第二优先级)

**绝对禁止：**
- 在 main/master 分支直接实现（需用户明确同意）
- 跳过任一审查环节
- 有未修复问题时继续
- 并行分发多个实现 subagent（会冲突）
- 让 subagent 自己读取计划文件（应提供完整文本）
- 跳过场景设置上下文
- 忽略 subagent 问题
- "差不多就行"的规格合规

**Iteration Cap：** 审查循环（发现→修复→再审查）直到批准，不跳过重新审查。

## 与其他技能的关系

| 对比项 | Subagent-Driven | Executing Plans |
|--------|-----------------|-----------------|
| 会话 | 同一会话（无上下文切换） | 单独会话 |
| 执行 | 每个任务 fresh subagent | 连续执行 |
| 审查 | 两阶段自动检查点 | 手动检查点 |
| 迭代速度 | 快（无人力介入） | 较慢 |
| 适用场景 | 同会话、独立任务 | 平行会话、连续执行 |

**前置技能：**
- `using-git-worktrees` — 必须先设置隔离工作区
- `writing-plans` — 创建本技能执行的计划

**后置技能：**
- `finishing-a-development-branch` — 所有任务完成后完成开发分支

## Red Flags

**Subagent 状态处理：**

| 状态 | 处理方式 |
|------|----------|
| **DONE** | 进入 spec 合规审查 |
| **DONE_WITH_CONCERNS** | 读concerns，正确性/范围问题先解决再审查 |
| **NEEDS_CONTEXT** | 提供缺失上下文，重新分发 |
| **BLOCKED** | 评估阻塞原因：更多上下文/更强模型/拆分任务/上报人类 |

**Never force same model retry without changes** — subagent 说卡住了，必须改变点什么。

**审查发现问题的正确流程：**
```
Reviewer: 发现问题
    ↓
Implementer 修复
    ↓
Reviewer 重新审查
    ↓
重复直到批准
```
