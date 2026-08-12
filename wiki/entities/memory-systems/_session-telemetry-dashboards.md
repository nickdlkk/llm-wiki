---
title: Session Telemetry & Cost Dashboards（区分性记录）
type: concept
created: 2026-08-12
updated: 2026-08-12
sources:
tags:
  - dashboard
  - telemetry
  - cost
  - session-analysis
  - concept
confidence: high
---

# Session Telemetry & Cost Dashboards（区分性记录）

> **本 wiki 收录「记忆系统」时主动排除的一类**。它们做 session 数据的**只读可视化/成本监控**，**不提取 memory / skill / fact**。本 wiki 主域是「提取」，不是「监控」，但因为常被搜索到一起，单独记录以便区分。

## 为什么单独列

调研过程中 `sort=stars` 搜索和关键词搜索会带回大量「Claude Code 用量统计 / token 成本 dashboard」类项目。这些**不是** session-extraction 工具，不应在 [[Session-Extraction-Pipeline]] 或 [[Memory-Systems]] 出现，但用户搜索时大概率一起找到——所以这页是「这是 dashboard，不是 miner」的**反向索引**。

## 识别特征

一个项目如果以下都满足，**几乎肯定不是**本 wiki 主域：

- ☐ 不持久化 memory / skill / fact
- ☐ 不修改任何状态文件（CLAUDE.md / .claude/skills/ / vault）
- ☐ 不调用 LLM 提取
- ☐ 主要输出 = token 用量 / 成本 / 工具调用统计 / 时间线
- ☐ README 关键词 = dashboard / cost / token / usage / telemetry / health

## 已知项目（不全，持续观察）

| 仓库 | ⭐ | 定位 |
|------|-----|------|
| LemonHound/claude-hooks-analytics | 3 | Claude Code telemetry hooks + token 用量 + browser dashboard |
| berenceylan/claude-radar | 2 | Executive dashboard for Claude Code usage & token cost |
| fayssal-elmofatiche/claudio | 0 | Claude I/O — Cost tracking, session intelligence, code impact |
| davidzohar/CodexAnalysisDashboard | 0 | Local browser dashboard for Codex JSONL session logs |
| etdofreshai/claude-session-analysis | 0 | Web dashboard analyzing Claude Code & Codex CLI: cost, tokens, models, effort, tool usage, timelines |
| dhrod0325/claude-monitor | 0 | Claude Code session monitor with real-time tracking, usage analytics |
| kateliemandt/alpha-session-survey-analysis | 0 | Alpha School · Session Survey Analyzer — 3 HTML dashboards from raw survey CSVs |
| 458187375-ship-it/codeburn-session-analyzer | 1 | Local-first Codex session analysis with optional CodeBurn metrics |
| faizlee/codex-session-timeline-analyzer | 1 | Read-only Codex Desktop JSONL timeline + bottleneck reports |
| zaharsyahrafi/cxusage | 1 | Codex daily usage from session logs, aggregating token counts |
| rubens-amaral/codex_usage_report | 4 | Go CLI to analyze OpenAI Codex session logs and estimate cooldowns |
| ccusage/ccusage | (multi-CLI) | Hermes + Claude Code + Codex + OpenClaw 等 13+ agent CLI 的 token/cost 报告 |

## 边界案例：什么时候该升格到本 wiki 主域

- 项目里**加了 LLM 提取**  → 升格为 miner
- 项目里**能修改 CLAUDE.md / skill**  → 升格为 miner
- 项目里**维护一个 vault / KB**  → 升格为 miner

## 调研方法备注

调研时**主动排除**了这一类。判断标准：项目首页**第一屏**出现 dashboard / cost / token 关键词 + 完全没有 LLM 调用描述 → 标记为「telemetry only」，不入 [[Memory-Systems]] / [[Session-Extraction-Pipeline]]，记到这里供未来人理解「我为什么没收录 X」。

## 相关页面

- [[Session-Extraction-Pipeline]] — 本 wiki 主域
- [[Memory-Systems]] — 记忆系统总览
- [[claude-code-trace]] — 本 wiki 收录的 session viewer 路线（Rust 三形态，含 subagent）
