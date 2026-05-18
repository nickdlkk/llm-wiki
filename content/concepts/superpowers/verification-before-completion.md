---
title: Verification Before Completion
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Verification Before Completion

> Source: skills/verification-before-completion/SKILL.md

## 核心工作流

### 证据门禁（The Gate Function）

```
BEFORE 声称任何状态或表达满意：

1. IDENTIFY: 什么命令能证明这个声明？
2. RUN: 执行完整命令（全新、完整）
3. READ: 完整输出，检查退出码，统计失败数
4. VERIFY: 输出是否确认声明？
   - 如果 NO: 用证据陈述实际状态
   - 如果 YES: 用证据陈述声明
5. ONLY THEN: 做出声明

跳过任何步骤 = 撒谎，不是验证
```

### 回归测试的特殊要求

TDD Red-Green 循环的验证：
```
✅ 写 → 跑（通过）→ 回滚修复 → 跑（MUST FAIL）→ 恢复 → 跑（通过）
❌ "我写了回归测试"（没有红绿验证）
```

## 铁律 / HARD-GATE

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

如果还没在这个消息中运行验证命令，不能声称通过。

## 与其他技能的关系

- **test-driven-development**：TDD 的红绿验证是本技能的组成部分
- **systematic-debugging**：修复后必须用证据验证才能声称成功
- **finishing-a-development-branch**：分支完成前必须验证测试

## 常见声明与要求

| 声明 | 需要 | 不充分 |
|------|------|--------|
| 测试通过 | 测试命令输出：0 failures | 上次运行、"应该通过" |
| Linter 干净 | Linter 输出：0 errors | 部分检查、推测 |
| 构建成功 | 构建命令：exit 0 | Linter 通过、日志看起来好 |
| Bug 修复 | 测试原始症状：通过 | 代码改了、假设修复了 |
| 回归测试工作 | 红绿循环验证 | 测试通过一次 |
| Agent 完成任务 | VCS diff 显示变更 | Agent 报告"成功" |
| 需求满足 | 逐行检查清单 | 测试通过 |

## Red Flags

- 使用 "应该"、"可能"、"似乎"
- 验证前表达满意（"很好！"、"完美！"、"完成！"）
- 未验证就 commit/push/PR
- 信任 agent 成功报告
- 依赖部分验证
- 认为"就这一次"
- 累了想快点完成
- **任何暗示成功但未运行验证的措辞**

## 常用验证命令模式

**测试：**
```
✅ [Run test command] [See: 34/34 pass] "All tests pass"
❌ "Should pass now" / "Looks correct"
```

**构建：**
```
✅ [Run build] [See: exit 0] "Build passes"
❌ "Linter passed"（linter 不检查编译）
```

**Agent 委托：**
```
✅ Agent reports success → Check VCS diff → Verify changes → Report actual state
❌ Trust agent report
```

## 常见合理化借口

| 借口 | 现实 |
|------|------|
| "现在应该工作了" | 运行验证 |
| "我有信心" | 信心 ≠ 证据 |
| "就这一次" | 无例外 |
| "Linter 通过了" | Linter ≠ 编译器 |
| "Agent 说成功了" | 独立验证 |
| "我累了" | 疲劳 ≠ 借口 |
| "部分检查就够了" | 部分什么都证明不了 |
| "换个说法规则就不适用" | 精神重于字面 |
