---
title: Chorus
type: entity
created: 2026-04-16
updated: 2026-04-16
sources:
  - https://github.com/Chorus-AIDLC/Chorus
  - https://www.v2ex.com/t/1205752
tags:
  - agent-harness
  - task-management
  - review-pattern
---

# Chorus

> Agent Harness for AI-Human Collaboration，参考 AWS AI-DLC（AI-Driven Development Lifecycle）

## 基本信息

- **Stars**: 556
- **语言**: TypeScript
- **URL**: https://github.com/Chorus-AIDLC/Chorus
- **License**: AGPL-3.0
- **技术栈**: Next.js + Prisma + PostgreSQL
- **创始者**: fennu2333

## 解决的问题

人变成了在 Asana 和终端之间来回搬运文本的 adapter — Agent 不知道该干什么，干完了不知道去哪交差。

## 核心设计：给 Agent 一个它自己能用的任务管理器

Agent 自己领任务、做任务、交任务，不靠人肉搬运。

## 演进历史

### v0.1（2月底）：先让 Agent 自己看到活

管道跑通：Idea → Proposal → Task → Execute → Verify

- 加了"反转对话"设计：人只抛粗糙想法，PM Agent 主动提问把需求聊清楚，然后自己出方案、拆任务，审批通过后 Developer Agent 去领活
- Claude Code 插件自动注入当前项目里还有哪些活要干、哪些任务被分配给了你

### v0.4（3月中）：Agent 太容易"自以为做完了"

- 加验收标准 checklist（每个 Task 有独立 AC 列表，Agent 做完必须逐条自检 pass/fail）
- 依赖强制执行（上游任务没验收通过，下游任务 API 不返回）

### v0.6（4月初）：让 Agent 审 Agent

关键决策：利用 Claude Code 插件机制定义独立的 reviewer agent：
- `proposal-reviewer`：审方案
- `task-reviewer`：审代码
- 审查记录永久存档，最多三轮不过才上报人类

### v0.6.1（上周）：/yolo 全串起来

- 一句话进去，Agent 自己走完整条管道，中间不问你一句话
- Ctrl+C 随时能断，已创建任务都持久化
- 某个 Task 反复过不了审，标记需要人工介入然后继续推进其他 Task

## 实际效果

- 中等复杂度 feature（3-8 个 task）成功率比预期高
- 调试思路变了：直接翻 Elaboration 记录和 Proposal 审查历史
- 时间释放：主要在想下一个 feature 做什么

## 参见

- [[Agent Review Pattern]] — 详细解释 Chorus 的审查者模式
- [[Harness Engineering]] — Chorus 实现了 Harness 的五大子系统
