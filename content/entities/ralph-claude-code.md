---
title: ralph-claude-code
type: entity
created: 2026-04-27
updated: 2026-04-27
sources:
  - https://github.com/frankbria/ralph-claude-code
tags:
  - harness-engineering
  - autonomous-loop
  - claude-code
  - bash
---

# ralph-claude-code

**frankbria/ralph-claude-code** — Bash 实现的 Autonomous AI Development Loop 系统，基于 Geoffrey Huntley 的 [Ralph Wiggum technique](https://ghuntley.com/ralph/)。v0.11.5，MIT License，566 tests。

## Core Concept

Ralph 将 Claude Code 包装在一个持久自参照循环中，自动执行直到项目完成，同时内置多重保护机制防止无限循环和 API 滥用。

## Architecture

```
ralph_loop.sh  ← 主循环脚本
    lib/
      circuit_breaker.sh   ← 三态熔断：CLOSED/HALF_OPEN/OPEN
      response_analyzer.sh  ← 响应分析 + JSON/text 双模式解析
      rate_limiter.sh       ← API 调用频率控制
      session_manager.sh    ← Session 连续性（24h 过期 + resume）
      task_sources.sh       ← 从 beads/GitHub Issues/PRDs 导入任务
```

## Exit Gate Mechanism（关键创新）

Dual-condition exit：必须同时满足两个条件才退出：
1. `completion_indicators >= 2`（启发式检测）
2. Claude 显式 `EXIT_SIGNAL: true`

JSON 模式和 Text 模式行为不同：JSON 模式下纯依赖 `EXIT_SIGNAL`；Text 模式下依赖 `confidence_score >= 70` + `has_completion_signal`。

## Key Safeguards

| Mechanism | Purpose |
|-----------|---------|
| Circuit Breaker | 3 次无进展 / 5 次同类错误 → OPEN 状态 |
| Rate Limiter | 100 calls/hour（可配 token budget） |
| 5h API Limit | 三层检测 + unattended 模式自动等待 |
| File Protection | 启动前校验 `.ralph/` 目录完整性 |
| Session Expiry | 24h 自动重置，防止 stale context |

## 与 open-ralph-wiggum 的区别

| | ralph-claude-code | open-ralph-wiggum |
|--|------------------|-------------------|
| Language | Bash | Bun + TypeScript |
| Author | frankbria | — |
| Exit Detection | Dual-condition gate | 各自实现 |
| Session Mgmt | 内置，24h expiry | 各自实现 |
| License | MIT | — |

## Relevance to Harness Engineering

Ralph 是 Harness Engineering 的最小化参考实现之一：
- **Feedback Loop**：loop→analyze→exit 三段式
- **Quality Gate**：dual-condition exit gate 是显式质量闸门
- **Rate Limiting**：防止 agent 失控消耗资源
- **Circuit Breaker**：错误累计后的熔断机制

是学习 autonomous loop 设计的良好起点。

---

## See also

- [[open-ralph-wiggum]] — Bun/TypeScript 实现的 Ralph Wiggum technique
- [[Harness Engineering]] — Ralph 的工程化语境
- [[Agentic Loop]] — Ralph 遵循的 Gather→Act→Verify 模式
