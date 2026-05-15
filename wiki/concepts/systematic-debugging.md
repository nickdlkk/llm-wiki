---
title: "systematic-debugging"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/systematic-debugging/SKILL.md
  - raw/superpower-with-files/skills/systematic-debugging/root-cause-tracing.md
  - raw/superpower-with-files/skills/systematic-debugging/defense-in-depth.md
  - raw/superpower-with-files/skills/systematic-debugging/condition-based-waiting.md
tags:
  - skills
  - debugging
  - root-cause
  - TDD
  - superpower-with-files
---

# systematic-debugging

> **铁律**：`NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`。随机修复浪费且制造新 bug。在提出修复前必须找到根因。

## 何时使用

用于**任何**技术问题：
- 测试失败
- 生产 bug
- 意外行为
- 性能问题
- 构建失败
- 集成问题

**尤其使用当：**
- 时间压力大（紧急情况容易猜测）
- "就一个 quick fix"看起来很明显
- 已经尝试多种修复
- 之前的修复没用
- 不完全理解问题

## 四阶段

### Phase 1: 根因调查

**在尝试任何修复前：**

1. **仔细读错误信息**
   - 不跳过 errors 或 warnings
   - 通常包含准确解决方案
   - 读完 stack traces
   - 记录行号、文件路径、错误码

2. **一致地复现**
   - 能可靠触发吗？
   - 具体步骤是什么？
   - 每次都发生？
   - 如果不可复现 → 收集更多数据，不要猜测

3. **检查最近变更**
   - 什么变更可能引起？
   - Git diff，最近提交
   - 新依赖、配置变更
   - 环境差异

4. **多组件系统中的证据收集**

   **当系统有多个组件时（CI → build → signing, API → service → database）：**
   ```bash
   # Layer 1: Workflow
   echo "=== Secrets available in workflow: ==="
   echo "IDENTITY: ${IDENTITY:+SET}${IDENTITY:-UNSET}"

   # Layer 2: Build script
   echo "=== Env vars in build script: ==="
   env | grep IDENTITY || echo "IDENTITY not in environment"

   # Layer 3: Signing script
   echo "=== Keychain state: ==="
   security list-keychains
   security find-identity -v
   ```
   每层加诊断日志，找出**哪一层**失败。

5. **追踪数据流**

   错误在调用栈深处？
   - 坏值从哪里来？
   - 谁用坏值调用的？
   - 追踪到源头
   - 在源头修复，不是症状

### Phase 2: 模式分析

**修复前找模式：**

1. 找类似工作的代码
2. 完全读参考实现（不要 skim）
3. 识别差异
4. 理解依赖

### Phase 3: 假设和测试

**科学方法：**

1. **形成一个假设**："我认为 X 是根因因为 Y"
2. **最小化测试**：做最小改变来测试假设
3. **验证再继续**：成功 → Phase 4；失败 → 新假设
4. **不知道时**：说"我不理解 X"，不要假装知道

### Phase 4: 实施

1. **创建 Failing Test Case** — 最简单的复现
2. **实施单一修复** — 针对识别的根因，一次改一个
3. **验证修复** — 测试通过？无回归？
4. **修复不工作？** 停止，数数已试几个。如果 < 3 → 回 Phase 1；如果 ≥ 3 → 质疑架构

### Phase 4.5: 3+ 修复失败时质疑架构

**表明架构问题的模式：**
- 每个修复在不同地方暴露新的共享状态/耦合/问题
- 修复需要"大规模重构"来实现
- 每个修复在其他地方产生新症状

**停止并质疑基础：**
- 这个模式根本上是合理的吗？
- 我们是在靠惯性坚持吗？
- 应该重构架构 vs. 继续修复症状？

## 红线

- "先 quick fix，以后调查"
- "直接试试改 X 看看是否 work"
- "一次改多个，运行测试"
- "跳过测试，我手动验证"
- "可能是 X，我 fix 那个"
- **"再来一次修复"（已试 2+ 次）**
- **每个修复在不同地方暴露新问题**

**全部意味着：停止。回 Phase 1。**

## 用户重定向信号

- "没发生吗？" — 你假定了没验证
- "会给我们看...吗？" — 应该加证据收集
- "停止猜测" — 你在没理解前提修复
- "Ultrathink this" — 质疑基础，不只是症状
- "我们卡住了？"（受挫）— 你的方法不 work

**看到这些：停止。回 Phase 1。**

## 常见合理化

| 借口 | 真相 |
|------|------|
| "问题简单，不需要流程" | 简单问题也有根因 |
| "紧急，没时间流程" | 系统调试比猜快 |
| "先试试这个再调查" | 第一个修复定模式。从开始就做对。 |
| "修复后写测试确认 work" | 未测试的修复不 stick |
| "一次多修复省时间" | 不能隔离哪个有效。制造新 bug。 |

## 支持技术

- **`root-cause-tracing.md`** — 沿调用栈向后追踪 bug，找到原始触发点
- **`defense-in-depth.md`** — 找到根因后在多层加验证
- **`condition-based-waiting.md`** — 用条件轮询替代任意超时

## 在 superpower-with-files 中的角色

systematic-debugging 是**质量门禁**，与 TDD 互补。TDD 防止 bug，systematic-debugging 确保修复的正确性。两者结合：先写 failing test，再按系统调试流程找到根因，最后修复。
