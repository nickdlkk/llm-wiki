---
title: "receiving-code-review"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/receiving-code-review/SKILL.md
tags:
  - skills
  - code-review
  - technical-rigor
  - YAGNI
  - superpower-with-files
---

# receiving-code-review

> 收到代码审查反馈后，实现建议前使用。需要技术严谨和核实，不是表演性同意或盲目实现。

## 响应模式

```
收到代码审查反馈时：

1. READ: 完全读反馈不反应
2. UNDERSTAND: 用自己的话重述要求（或问）
3. VERIFY: 对照代码库实际检查
4. EVALUATE: 对这个代码库技术上合理吗？
5. RESPOND: 技术确认或有理由的反驳
6. IMPLEMENT: 一次一项，测试每个
```

## 禁止响应

**绝不：**
- "You're absolutely right!"（违反 CLAUDE.md）
- "Great point!" / "Excellent feedback!"（表演性）
- "Let me implement that now"（核实前）

**代替：**
- 重述技术要求
- 问澄清问题
- 用技术理由反驳
- 直接开始工作（行动 > 言语）

## 处理不清晰反馈

```
如果有不清楚的项目：
  停止 — 不要实现任何东西
  先问澄清

为什么：项目可能相关。半理解 = 错误实现。
```

## 来源处理

### 来自用户
- **可信** — 理解后实现
- **仍问**如果范围不清
- **无表演性同意**
- 直接到行动或技术确认

### 来自外部审查者

```
实现前检查：
  1. 技术上对这个代码库正确？
  2. 破坏现有功能？
  3. 当前实现有理由？
  4. 所有平台/版本都 work？
  5. 审查者理解完整上下文？

如果建议看起来错了：
  用技术理由反驳

如果不能轻松核实：
  说："没有 X 我无法核实。应该 [investigate/ask/proceed]？"

如果冲突用户之前的决策：
  先与用户讨论
```

**用户规则**："External feedback — be skeptical, but check carefully"

## YAGNI 检查

```
如果审查者建议"properly implementing"某功能：
  grep 代码库实际用法

  如果未使用："这个 endpoint 没被调用。移除它（YAGNI）？"
  如果使用了：then properly implement
```

**用户规则**："审查者和你都向用户汇报。如果不需要这个功能，不要加。"

## 实现顺序

```
多项目反馈：
  1. 先澄清任何不清晰
  2. 然后按此顺序实现：
     - Blocking issues（breaks, security）
     - Simple fixes（typos, imports）
     - Complex fixes（refactoring, logic）
  3. 每次单独测试
  4. 验证无回归
```

## 何时反驳

反驳当：
- 建议破坏现有功能
- 审查者缺乏完整上下文
- 违反 YAGNI（未用功能）
- 技术上对这个技术栈不正确
- 存在遗留/兼容性原因
- 冲突用户架构决策

## GitHub 回复

在 comment thread 中回复内联审查评论（`gh api .../comments/{id}/replies`），不要作为顶层 PR 评论。

## 在 superpower-with-files 中的角色

receiving-code-review 与 requesting-code-review 配对，确保审查反馈被正确处理：
- requesting 发起审查
- receiving 处理反馈：核实 → 评估 → 反驳或实现

它是防止"审查通过但代码仍有问题"的关键环节。
