---
title: "verification-before-completion"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/verification-before-completion/SKILL.md
tags:
  - skills
  - verification
  - quality-gate
  - evidence
  - superpower-with-files
---

# verification-before-completion

> **铁律**：`NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE`。未运行验证命令不得声称状态，证据先于断言。

## Gate Function

```
在声称任何状态或表达满意前：

1. IDENTIFY: 什么命令能证明这个声明？
2. RUN: 执行完整命令（全新、完整）
3. READ: 完整输出，检查 exit code，计数失败
4. VERIFY: 输出是否确认声明？
   - 如果否：用证据说明实际状态
   - 如果是：用证据说明声明
5. 只有这时：做出声明
```

跳过任何步骤 = 作弊，不是验证。

## 常见失败

| 声明 | 需要 | 不充分 |
|-------|------|--------|
| Tests pass | Test command output: 0 failures | 上次运行、"应该 pass" |
| Linter clean | Linter output: 0 errors | 部分检查、外推 |
| Build succeeds | Build command: exit 0 | Linter pass、日志看起来好 |
| Bug fixed | Test original symptom: passes | 代码改了，假定修复 |
| Regression test works | Red-green cycle verified | Test pass 一次 |
| Agent completed | VCS diff shows changes | Agent 报告"成功" |
| Requirements met | Line-by-line checklist | Tests passing |

## 红线

- 用 "should", "probably", "seems to"
- 验证前表达满意（"Great!", "Perfect!", "Done!"）
- 提交/push/PR 前未验证
- 信任 agent 成功报告
- 依赖部分验证
- 想"就这一次"
- 累了想结束
- **任何措辞暗示未运行验证就成功**

## 正确 vs 错误

```
✅ [Run test command] [See: 34/34 pass] "All tests pass"
❌ "Should pass now" / "Looks correct"

✅ [Run build] [See: exit 0] "Build passes"
❌ "Linter passed" (linter doesn't check compilation)

✅ Agent reports success → Check VCS diff → Verify changes → Report actual state
❌ Trust agent report
```

## 为什么这重要

从 24 个失败记忆：
- 用户说"我不信你" — 信任破坏
- 未定义函数 shipped — 会 crash
- 缺失需求 shipped — 功能不完整
- 虚假完成的时间浪费 → 重定向 → 返工
- 违反："诚信是核心价值。撒谎会被替换。"

## 在 superpower-with-files 中的角色

verification-before-completion 是**最后一道门禁**。它嵌入在所有其他 skill 中——spf-exec-plan 执行 checkpoint、finishing-a-development-branch 合并前、subagent-driven-development 任务完成前都必须验证。没有它，前面的 TDD 和系统调试成果会在最后一步被破坏。
