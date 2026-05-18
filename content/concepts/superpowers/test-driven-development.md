---
title: Test-Driven Development
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Test-Driven Development

> Source: skills/test-driven-development/SKILL.md

## 核心工作流

### RED-GREEN-REFACTOR 循环

```
RED → Write Failing Test → Verify Fails Correctly
  ↓                                      ↓
REFACTOR ← Verify Stays Green ← GREEN → Verify Passes
  ↓
Next RED (repeat)
```

**RED 阶段：**
- 写一个最小测试，展示期望行为
- 测试命名清晰，测试真实行为而非 mock
- 必须能触发失败

**GREEN 阶段：**
- 用最简代码通过测试
- 不添加额外功能（YAGNI）
- 不做重构或"改进"

**REFACTOR 阶段：**
- 通过绿色后：移除重复、改善命名、提取辅助函数
- 保持测试绿色，不添加行为

### 验证检查清单

完成前必须确认：
- [ ] 每个新函数/方法都有测试
- [ ] 每个测试在实现前亲眼看到失败
- [ ] 失败原因正确（功能缺失，非拼写错误）
- [ ] 每个测试只写了最小代码通过
- [ ] 所有测试通过
- [ ] 输出干净（无错误、无警告）
- [ ] 使用真实代码（mock 仅在必要时）

## 铁律 / HARD-GATE

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

**无例外规则：**
- 先写代码再写测试？删除。从头开始。
- 不要保留作为"参考"
- 不要"改编"它来写测试
- 删除意味着彻底删除

违反规则字面即违反精神。

## 与其他技能的关系

- **systematic-debugging**：发现 bug 时，先写失败的测试复现 bug，再遵循 TDD 循环
- **verification-before-completion**：完成前必须验证所有测试通过
- **testing-anti-patterns**（同目录）：添加 mock 或测试工具时，参阅避免常见陷阱

## Red Flags

- 先写代码后写测试
- 测试在实现后添加
- 测试立即通过
- 无法解释测试为何失败
- "just this once" 的合理化
- "我已经手动测试过了"
- "测试后写能达到同样目的"
- "保持作为参考"或"改编现有代码"
- "已经花 X 小时了，删除太浪费"
- "TDD 是教条，我很务实"

**以上全部意味着：删除代码，用 TDD 重新开始。**

## Testing Anti-Patterns 参考

同目录的 `@testing-anti-patterns.md` 涵盖：
- 测试 mock 行为而非真实行为
- 向生产类添加仅测试方法
- 不理解依赖关系就 mock

## 常见合理化借口

| 借口 | 现实 |
|------|------|
| "太简单不需要测试" | 简单代码也会坏，测试只需30秒 |
| "我之后测" | 测试立即通过什么都证明不了 |
| "之后测试也能达到同样目标" | 后测 = "这做什么？"，先测 = "应该做什么？" |
| "已经手动测试了" | 临时 ≠ 系统化，无记录，不能重跑 |
| "删除 X 小时工作太浪费" | 沉没成本谬误，保留无法信任的代码是技术债务 |
| "探索一下再说" | 可以，抛弃探索，从 TDD 开始 |
| "TDD 会拖慢我" | TDD 比调试快，务实 = 测试优先 |
