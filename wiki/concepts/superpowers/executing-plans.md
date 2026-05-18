---
title: Executing Plans
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Executing Plans

> Source: skills/executing-plans/SKILL.md

## 核心工作流

**3步流程：**

```
┌────────────────────────────────────────────────────────────┐
│  Step 1: Load and Review Plan                              │
│  ├── Read plan file                                        │
│  ├── Review critically (identify questions/concerns)      │
│  └── If concerns → raise before starting                  │
│      If no concerns → create TodoWrite and proceed         │
└────────────────────────────────────────────────────────────┘
                            ↓
┌────────────────────────────────────────────────────────────┐
│  Step 2: Execute Tasks                                     │
│  ├── Mark as in_progress                                  │
│  ├── Follow each step exactly                             │
│  ├── Run verifications as specified                        │
│  └── Mark as completed                                     │
└────────────────────────────────────────────────────────────┘
                            ↓
┌────────────────────────────────────────────────────────────┐
│  Step 3: Complete Development                              │
│  └── Use superpowers:finishing-a-development-branch        │
└────────────────────────────────────────────────────────────┘
```

## 铁律 / HARD-GATE

**绝对禁止：** 未经用户明确同意，在 main/master 分支直接实现。

**立即停止并求助的情况：**
- 遇到阻塞（缺失依赖、测试失败、指令不明确）
- 计划有关键缺口阻止开始
- 不理解某个指令
- 验证反复失败

**原则：** 遇到问题时请求澄清，而非猜测。

**返回审查阶段的情况：**
- Partner 根据反馈更新了计划
- 基本方法需要重新思考

## 与其他技能的关系

**与 subagent-driven-development 的关系：**

| 场景 | 推荐技能 |
|------|----------|
| 有实现计划 + 任务相对独立 + 同一会话 | `subagent-driven-development` |
| 有实现计划 + 平行会话执行 | `executing-plans` |
| 任务紧密耦合 | 人工执行或先 brainstorm |

**前置技能：**
- `using-git-worktrees` — 必须先设置隔离工作区
- `writing-plans` — 创建本技能执行的计划

**后置技能：**
- `finishing-a-development-branch` — 所有任务完成后完成开发分支

## Red Flags

**不要做的事：**
1. 不批评性地审查计划就盲目执行
2. 跳过验证步骤
3. 遇到阻塞时强行推进
4. 跳过 plan 中指定引用的 skill
5. 在 main/master 分支直接实现

**Plan 有问题时：**
- 停下来提问
- 不要假设意图
- 让用户澄清后再继续

---

> **Note:** Superpowers 在有 subagent 支持的平台（如 Claude Code 或 Codex）上效果最佳。质量显著更高。优先考虑 `subagent-driven-development`。
