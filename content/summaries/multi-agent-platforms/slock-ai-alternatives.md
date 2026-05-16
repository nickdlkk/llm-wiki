---
title: slock.ai 竞品分析 — 开源替代方案
type: summary
created: 2026-05-08
sources: [slock.ai-official, multica-github, clawith-github, opencanopy-website, agentdm-website]
tags: [agent-collaboration, multi-agent, open-source-alternatives]
---

# slock.ai 竞品分析 — 开源替代方案

## 快速概览

**slock.ai** 定位：人类和 AI Agent 以平等队友身份在频道/私信中实时协作。

开源替代方向分两类：**任务管理型**（像管人一样管 Agent）和**通信协议型**（Agent ↔ Agent 直接对话）。

## 竞品对比

| 工具 | 类型 | Stars | 开源 | 自托管 | 核心特点 |
|------|------|-------|------|--------|---------|
| **slock.ai** | 协作平台（商业） | — | ❌ | ❌ | 成熟产品，实时协作 |
| **Multica** | 任务管理+协作 | 26k ⭐ | ✅ | ✅ | 最成熟，Jira 风格任务分配 |
| **Clawith** | 团队协作 | 3.5k ⭐ | ✅ | ✅ | OpenClaw for Teams，多 Agent 身份 |
| **Canopy** | 本地 P2P | — | 需确认 | ✅ | 端到端加密，无需云账号 |
| **AgentDM** | 通信协议 | — | 需确认 | ✅ | MCP/A2A 协议，Slack 双向集成 |
| **cc-connect** | IM Bridge | 7.9k ⭐ | ✅ | ✅ | 将 Agent 接入 IM 平台 |
| **golembot** | IM Bridge | 271 ⭐ | ✅ | ✅ | 支持 ClawHub 13k skills |

## 任务管理型首选：Multica

**26k ⭐，最接近 slock.ai 定位的开源替代**

- 给 Agent 分配 Jira ticket，像管人一样管 Agent
- 进度追踪、技能积累、自主执行
- 支持 Claude Code、Codex、OpenClaw、OpenCode、Hermes 等 10+ Agent
- 自托管，vendor-neutral

## 团队协作型：Clawith

**3.5k ⭐，OpenClaw for Teams**

- 多 Agent 持久身份 + 共享记忆
- 频道协作、任务协调
- 基于 OpenClaw 生态

## 本地优先：Canopy

- 无需云账号，端到端加密 P2P
- Slack 风格频道 + MCP server
- 适合隐私敏感场景

## 协议层：AgentDM

- MCP + A2A 双协议
- Claude ↔ OpenAI ↔ 任意 MCP Agent 互通
- 5 行 JSON 接入

## 结论

- **完整替代 slock.ai** → Multica（26k ⭐ 最成熟）
- **接入 IM** → cc-connect（7.9k ⭐）
- **隐私优先** → Canopy（本地 P2P）
- **协议互通** → AgentDM（MCP/A2A）
