---
title: Systematic Debugging
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Systematic Debugging

> Source: skills/systematic-debugging/SKILL.md

## 核心工作流

### 4阶段根因分析流程

```
Phase 1: Root Cause Investigation
    ↓ (理解 WHAT 和 WHY)
Phase 2: Pattern Analysis
    ↓ (找到模式和差异)
Phase 3: Hypothesis & Testing
    ↓ (形成假设，最小化测试)
Phase 4: Implementation
    ↓ (必要时返回 Phase 1)
    If 3+ fixes failed → Question Architecture
```

**Phase 1：根因调查（必须先完成）**
1. 仔细阅读错误信息（读完整栈追踪）
2. 一致地复现问题（能可靠触发？步骤？）
3. 检查最近变更（git diff、依赖、配置）
4. 多组件系统中：在每个组件边界添加诊断日志
5. 追踪数据流（从错误深处向上追溯到源头）

**Phase 2：模式分析**
1. 找相似工作中的代码作为参考
2. 与参考实现完整对比（读每一行）
3. 识别差异（列出所有不同）
4. 理解依赖和假设

**Phase 3：假设与测试（科学方法）**
1. 清晰陈述假设："我认为 X 是根因因为 Y"
2. 最小化测试（一次一个变量）
3. 验证后再继续（成功 → Phase 4，失败 → 新假设）

**Phase 4：实施**
1. 创建失败的测试用例（MUST HAVE）
2. 实现单一修复（针对根因）
3. 验证修复
4. **3+ 次修复失败 → 停止，质疑架构**

### 架构质疑信号

- 每次修复在不同位置暴露共享状态/耦合
- 修复需要"大规模重构"才能实现
- 每次修复在其他地方产生新症状

→ 停止，与 human partner 讨论后再继续

## 铁律 / HARD-GATE

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

未完成 Phase 1，不得提出任何修复。

**尤其不要跳过：**
- 问题看似简单（简单 bug 也有根因）
- 时间紧迫（匆忙保证返工）
- manager 要立即修复（系统化比盲目尝试快）

## 与其他技能的关系

- **test-driven-development**：Phase 4 Step 1 创建失败测试用例
- **verification-before-completion**：验证修复有效后再声称成功
- **root-cause-tracing.md**（同目录）：通过调用栈向后追踪 bug
- **defense-in-depth.md**（同目录）：找到根因后在多层添加验证
- **condition-based-waiting.md**（同目录）：用条件轮询替代任意超时

## 支持技术

同目录提供的技术：
- `root-cause-tracing.md` - 通过调用栈向后追踪 bug，找到原始触发点
- `defense-in-depth.md` - 找到根因后在多层添加验证
- `condition-based-waiting.md` - 用条件轮询替代任意超时

## Red Flags

停止并遵循流程的信号：
- "快速修复，先这样"
- "试试改 X 看是否有效"
- "加多个改动，跑测试"
- "跳过测试，我手动验证"
- "可能是 X，我来修"
- "不完全理解但这可能有效"
- "Pattern 说 X 但我换种方式"
- 在追踪数据流前提出解决方案
- **"再试一次"（已经试了2+次）**
- **每次修复在不同位置暴露新问题**

**以上全部意味着：停止。返回 Phase 1。**

## 快速参考

| 阶段 | 关键活动 | 成功标准 |
|------|----------|----------|
| 1. 根因 | 读错误、复现、检查变更、收集证据 | 理解 WHAT 和 WHY |
| 2. 模式 | 找工作示例、对比 | 识别差异 |
| 3. 假设 | 形成理论、最小化测试 | 确认或新假设 |
| 4. 实施 | 创建测试、修复、验证 | Bug 解决，测试通过 |

## 真实效率数据

- 系统化方法：15-30 分钟修复
- 随机尝试：2-3 小时折腾
- 首次修复率：95% vs 40%
- 引入新 bug：接近零 vs 常见
