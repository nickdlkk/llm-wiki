---
title: Vibe Kanban — 多 Coding Agent 并行调度方法论
date: 2026-04-23
tags:
  - AI-Coding
  - Multi-Agent
  - Engineering-Workflow
  - Vibe-Kanban
---

# Vibe Kanban — 多 Coding Agent 并行调度方法论

> 原文：[工程师如何把多个 Coding Agent 真正带起来](https://www.ixiqin.com/2026/04/23/how-engineers-can-truly-bring-together-multiple-coding/) by 白宦成

## 核心问题

当一个人同时使用 Claude Code、Codex、OpenCode 等多个 Coding Agent 时，真正的瓶颈不再是 AI 的代码能力，而是**人的并发调度能力**：

1. 手头有 100 个任务，每个都是 P0
2. 知道每个任务怎么做，但只有一个自己
3. 最多同时盯 3-4 个窗口，无法规模化扩容

## Terminal UI 的局限

Claude Code / Codex 这类 Terminal UI 本质是**单线程 Chat Bot**：
- 多任务 → 靠 Terminal 多 Tab 或 tmux → 人成为瓶颈
- 上下文容易混：修 bug 时顺手改了另一个功能，最后分不清对话是为哪个问题
- 计划与执行黏在一起：任务还没定义清楚，AI 就已经开始跑了
- Review 成本递增：开更多 Agent ≠ 更多工程效率，反而产生更多需要人收拾的 diff

## Vibe Kanban 的解法

不是新的 Coding Agent，而是一个**把 Planning、Workspace、Review、Preview、PR 流程串联起来的管理层**。

核心流程：
1. 先拆 issue
2. 再明确计划
3. 再开 workspace
4. 再让不同 agent 去执行
5. 再 review diff
6. 再决定是否收敛、合并

## 四个关键设计

### 1. Kanban 机制
熟悉的 TODO / Doing / DONE 看板，让任务状态一目了然，收敛每个任务的上下文，减少认知负担。

### 2. 强制 Plan Mode
人类不擅长把任务交代清楚，AI 也不擅长追问 → 让 AI 强制开启 Plan Mode，主动发现歧义并主动对话消除歧义。

### 3. Agent 并发 + Git Worktree 集成
同时启动 N 个 Agent 并行处理，人始终处于**决策、确认、检查**环节。同时自动创建 Git Worktree 分支，任务完成后 merge 回 main，分支冲突由 AI 解决。

### 4. 幽默感
任务完成时用牛叫提醒——"AI 牛马把活干完了"。

## 适合谁 / 不适合谁

**适合：**
- 软件工程师，知道每个任务怎么做，但缺乏并行带宽
- 需要从串行转并行的人

**不适合：**
- 不会拆任务的人
- 把 AI 当自动许愿机的人
- 只想偶尔用一下 agent 的轻量用户
- 还没有 code review 能力的人

## Sunset 背景

Vibe Kanban 母公司 Bloop AI 已宣布 Shutdown，项目转向 Open Source & community maintained。其方向是正确的，代表一种易于理解的 Agent Scale 实现方式。

## 核心结论

> Vibe Kanban 真正解决的是：让你变成一个"包工头"而非"执行者"——不是变成 10x 工程师，而是变成 10x 分发任务的人。
