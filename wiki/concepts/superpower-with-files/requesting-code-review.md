---
title: "requesting-code-review"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/requesting-code-review/SKILL.md
  - raw/superpower-with-files/skills/requesting-code-review/code-reviewer.md
tags:
  - skills
  - code-review
  - quality-gate
  - superpower-with-files
---

# requesting-code-review

> 完成任务、实现重要功能、或合并前验证工作时，发起代码审查请求。

**核心原则**：早审查，常审查。

## 何时请求审查

**强制：**
- Subagent-driven development 中每任务完成后
- 重要功能完成后
- 合并到 main 前

**可选但有价值：**
- 卡住时（fresh perspective）
- 重构前（baseline check）
- 复杂 bug 修复后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 调度 code-reviewer subagent：**

使用 Task tool，填充 `code-reviewer.md` 模板中的占位符：
- `{WHAT_WAS_IMPLEMENTED}` — 刚构建了什么
- `{PLAN_OR_REQUIREMENTS}` — 应该做什么
- `{BASE_SHA}` — 起始 commit
- `{HEAD_SHA}` — 结束 commit
- `{DESCRIPTION}` — 简要描述

**3. 行动反馈：**
- 立即修复 Critical 问题
- 继续前修复 Important 问题
- 记录 Minor 问题供后续
- 如果 reviewer 错了（有理由）反驳

## 示例

```
[刚完成 Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch superpowers:code-reviewer subagent]
  WHAT_WAS_IMPLEMENTED: Verification and repair functions for conversation index
  PLAN_OR_REQUIREMENTS: Task 2 from docs/plans/deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Fix progress indicators]
[Continue to Task 3]
```

## 红线

**绝不：**
- 因为"简单"跳过审查
- 忽略 Critical 问题
- 有未修复 Important 问题继续
- 对有效技术反馈争辩

## 在 superpower-with-files 中的角色

requesting-code-review 是 **subagent-driven-development 的嵌入审查点**——每任务完成后立即触发。它与 receiving-code-review 配对使用：
- requesting：发起审查请求
- receiving：处理反馈并核实

两者形成完整的审查闭环，防止低质量代码进入下游。
