---
title: Dispatching Parallel Agents
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Dispatching Parallel Agents

> Source: skills/dispatching-parallel-agents/SKILL.md

## 核心工作流

**核心原则：** 每个独立问题域派发一个 Agent，并发执行。

### 决策树：何时用 / 何时不用

```
Multiple failures?
    ├── No → 单个 Agent 处理
    └── Yes → Are they independent?
              ├── No → 相关问题，一起调查
              └── Yes → Can they work in parallel?
                        ├── Yes → 并行派发
                        └── No → 顺序派发（共享状态冲突）
```

**适用场景：**
- 3+ 个测试文件失败，根因不同
- 多个子系统独立损坏
- 每个问题可独立理解，无需其他上下文

**不适用场景：**
- 问题相关（修一个可能解决其他）
- 需要理解完整系统状态
- 探索性调试（还不知道哪里坏了）

### Agent Prompt 结构

每个 Agent 的 prompt 必须满足 **Focused / Self-contained / Specific output**：

| 维度 | ❌ 错误示例 | ✅ 正确示例 |
|------|-----------|------------|
| **Scope** | "Fix all the tests" | "Fix agent-tool-abort.test.ts" |
| **Context** | "Fix the race condition" | 粘贴错误信息和测试名称 |
| **Constraints** | 无限制 | "Do NOT change production code" |
| **Output** | "Fix it" | "Return summary of root cause and changes" |

### 实际案例

**场景：** 6 个测试失败，分布在 3 个文件

| 文件 | 问题类型 | Agent 任务 |
|------|----------|-----------|
| agent-tool-abort.test.ts | 3 failures (timing) | Agent 1 |
| batch-completion-behavior.test.ts | 2 failures (tools not executing) | Agent 2 |
| tool-approval-race-conditions.test.ts | 1 failure (execution count = 0) | Agent 3 |

**结果：** 3 个问题并行解决，无冲突，全量测试通过。

## 铁律 / HARD-GATE

1. **独立问题域** — 每个 Agent 只处理一个独立问题域，不共享状态
2. **隔离上下文** — Agent 绝不继承主会话的 context，手动构造所需信息
3. **收敛后验证** — Agent 返回后必须：审查摘要 → 检查冲突 → 全量测试

## 与其他技能的关系

- **requesting-code-review**: 并行调查完成后，用 code-reviewer 验证修复
- **receiving-code-review**: 接收审查反馈时遵循 6 步模式
- **Subagent-Driven Development**: 每个 task 并行派发是核心模式

## Red Flags

| 错误 | 后果 |
|------|------|
| "Fix all the tests" | Agent 迷失，无法聚焦 |
| 不提供上下文 | Agent 不知道从何入手 |
| 无约束条件 | Agent 可能重构所有代码 |
| 输出模糊 | 不知道改了什么，无法验证 |
| 共享状态并行 | Agent 相互干扰，文件冲突 |
| 相关问题并行 | 修一个可能修好其他，白费功夫 |

