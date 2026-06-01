---
title: "Agent 是否需要分工以及角色划分？"
type: summary
created: 2026-06-01
updated: 2026-06-01
sources:
  - https://x.com/jolestar/status/2061252445901337048
tags:
  - Agent 协作
  - AI Coding
  - holon
---

> 📄 **原文**：[《Agent 是否需要分工以及角色划分？》](https://x.com/jolestar/status/2061252445901337048) — @jolestar

## 核心问题

一个 Agent 写完代码后，再让另一个 Agent 来 review，但经常出现**两个 Agent 越改越多、无法收敛**的情况，最后还是需要人工介入。

## 常见但有缺陷的方案

让两个 Agent 读同一个分支，把结果写在文档里，基于文档交流。

**根本问题**：两个 Agent 对各自以及对方的**角色认知并不一样**。Dev Agent 很容易把 Reviewer Agent 当成另一个 dev——双方都倾向于改代码而不是接受建议，角色边界模糊，导致无限迭代。

## jolestar 的方案：GitHub PR 工作流

> 这套机制已经包含在 LLM 的训练数据里，**Agent 天生就理解它的运作方式**。

- **Reviewer Agent** 明确知道自己的职责是审查 PR 而非写代码
- PR 的状态机（Open → Review → Merge / Request Changes）天然提供了**收敛机制**
- **Structural Separation**（结构分离）：reviewer 的输出是 approval/request-changes，而非代码补丁——职责边界清晰

## 技术背景

jolestar 是 [holon](https://github.com/holon-run/holon)（Headless Coding Agent Runner）的核心贡献者。holon 本身不是 Agent，而是一个**本地工作台**，为多个 Agent 提供持续的工作上下文：

- **Work-first task model**：任务组织成显式的 Work 而非散落在对话中
- **Event-driven wait/wake**：Agent 可以等待外部事件或人工确认，条件满足后恢复到对应的工作
- **Explicit context and trust boundaries**：区分操作者输入、外部事件、工具结果、执行轨迹，不同来源的信息不会混淆

holon 接收 GitHub Issues，输出 PR-ready patches 和 summary。

## 结论

Agent 协作的收敛性取决于**角色定义的清晰度**。用已有的、被 LLM 深刻理解的社会化流程（PR review）比人为创造一个文档交互层更可靠——LLM 对 PR 工作流的理解是内化的，不需要额外的约定或 prompt engineering。