---
title: gstack /investigate
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/investigate/SKILL.md"]
tags: [gstack, debugging, root-cause, systematic]
---

# /investigate

Systematic debugging with root cause investigation — 四阶段：investigate → analyze → hypothesize → implement。**铁律：未完成根因调查不得尝试修复**。与 gstack `/review` 的核心区别：review 是预防，investigate 是治疗。

## 核心职责

**何时触发**：
- 用户说 "debug this"、"fix this bug"、"why is this broken"、"investigate this error"、"root cause analysis"
- 用户报告 errors、500 errors、stack traces、"it was working yesterday"、或 troubleshooting 为什么某事停止工作
- **主动介入**：当用户描述错误/异常行为时，investigate skill 会主动触发（不直接修复）

**与 superpower-with-files 的 `systematic-debugging` 对比**：两者都是根因优先调试，但 gstack `/investigate` 集成了 freeze hook（调试时防止意外修改其他文件）和 gbrain 历史查询（是否曾经解决过同类问题）。

## 四阶段工作流

### Phase 1: Investigate（调查）

收集证据，不下结论：
- 读取错误日志、stack traces、相关代码
- 复现问题（如果可以）
- 查询 gbrain 历史：`~/.gstack/analytics/timeline` 中是否有同类问题的解决记录

### Phase 2: Analyze（分析）

从证据提炼关键信息：
- 哪些代码路径被执行了？
- 哪些输入触发了错误？
- 错误在调用栈的哪一层？

### Phase 3: Hypothesize（假设）

形成可验证的假设：
- "我认为是 X 导致了 Y"
- 假设必须可证伪（如果验证失败，需要新假设）

### Phase 4: Implement（实现）

只修复根因，不修症状：
- 一个 bug 一个根因
- 修复后重新运行调查，确认问题消失

## Freeze Hook（关键特性）

`/investigate` 绑定了 PreToolUse hook：

```
Edit/Writes → check-freeze.sh → 验证调试范围边界
```

防止在调试过程中意外修改冻结目录之外的文件。与 `/freeze` skill 联动。

## AskUserQuestion 门禁

在四阶段的每个 STOP 点，skill 都会通过 AskUserQuestion 暂停，等待人工确认方向。如果 AskUserQuestion 不可用（无 MCP 变体），skill 报告 `BLOCKED — AskUserQuestion unavailable`。

## 相关技能

- [[concepts/gstack/review]] — pre-landing 代码审查（review 预防 bug）
- [[entities/gstack]] — gstack 完整 entity 入口
