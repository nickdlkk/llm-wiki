---
title: "OpenAI Harness Engineering with Codex"
summary: OpenAI 用 Codex agent 构建了一个百万行代码的产品，核心发现是 harness（即环境、工具、反馈循环）决定了 agent 的上限，而非模型本身。
source: raw/articles/openai-harness-engineering-codex-20260211.md
author: Ryan Lopopolo
organization: OpenAI
date: 2026-02-11
tags: [harness-engineering, codex, agentic-ai, openai, software-engineering]
ingested: 2026-04-21
---

# OpenAI Harness Engineering with Codex

## 核心结论

OpenAI 的 Ryan Lopopolo 团队用 Codex agent 从零构建了一个近百万行代码的产品，3 人团队达到 3.5 PRs/工程师/天，且 throughput 随团队规模增长而非下降。**核心洞察：人类只负责设计环境、定义意图、建立反馈循环——代码全部由 agent 生成。**

## 五大关键发现

### 1. 从"写代码"到"搭环境"

Harness 工程师的核心工作不是写代码，而是：
- **设计环境**：让 agent 能直接操作应用 UI（git worktree per change）、日志（LogQL）、指标（PromQL）、Chrome DevTools
- **建立反馈循环**：让 agent 自己验证自己的输出（自动化测试、UI 驱动验证、性能查询）
- **捕获人类 taste**：不是通过 review 评论，而是通过编码成 invariant、linter 规则、golden principles

### 2. Repository 是唯一的知识来源

**给 agent 一张地图，而不是 1000 页手册。** 单个 AGENTS.md 会：context 稀缺导致任务被挤出 guidance 变成 non-guidance；信息过期变成 stale rule graveyard；无法做机械检查导致 drift。

正确的做法（OpenAI 实际验证）：
- AGENTS.md ≈ 100 行，作为目录（table of contents）
- 真实知识存在 `docs/` 目录中（design-docs/, exec-plans/, product-specs/, references/）
- 通过 linter + CI 强制知识库新鲜度和交叉链接
- 用 doc-gardening agent 定期扫描过期文档并自动开 PR 修复

### 3. Agent-legibility 是目标

Agent 无法访问的信息 = 不存在。Google Docs、Slack 讨论、人类头脑中的知识对 agent 不可见。**Repository 是唯一真实来源。** 技术的选择标准：能被 agent 完全内化和推理（boring tech > clever tech）。

### 4. 架构约束是早期前提

分层架构（Types → Config → Repo → Service → Runtime → UI）在传统软件工程中通常是"等团队大了再搞"。在 agent-first 开发中，这必须是早期前提：**约束使速度成为可能**。通过 custom linter 强制执行，错误信息直接注入 remediation 指令。

### 5. 持续垃圾回收 > 定期大扫除

AI slop 不会自动消失。人类每周花 20% 时间清理不scalable。正确做法：**golden principles**（opinionated mechanical rules）编码进 repo，background agent 持续扫描、评分、提 PR 修复。技术债务像高利贷——小步持续偿还优于到期一次性清理。

## 端到端自主阈值

在 OpenAI 的实验中，Codex 最近跨越了一个关键阈值：给定单个 prompt，agent 可以自主完成从 bug 复现到 merge 的完整流程：

```
validate → reproduce bug → record video(fail) → implement fix → 
validate via UI → record video(pass) → open PR → respond to reviews → 
merge (only escalate when judgment required)
```

## 与其他 Harness 来源的关系

| 框架 | 核心组件 | 重点 |
|------|---------|------|
| Perplexity（概念层） | Intent Spec / Harness / Agent | 定义 harness 的三层架构 |
| Anthropic（实现层） | Tools / Hooks / Subagents / MCP / Permissions / Sessions | Claude Code 的六大组件 |
| **OpenAI（实践层）** | Environment / Feedback loops / Architecture invariants / Knowledge map | **Harness 在真实产品中的具体工程实现** |

OpenAI 的文章是 Harness Engineering 领域目前最完整的实践报告，验证了 Perplexity 的概念框架在真实产品级规模下的有效性。
