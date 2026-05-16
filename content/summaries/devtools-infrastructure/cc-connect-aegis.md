---
title: cc-connect & Aegis — AI Coding Agent 工具双雄
type: summary
created: 2026-05-08
sources: [cc-connect-github, cc-connect-usage-zh-CN, Aegis-github]
tags: [agent-bridge, architecture-driven, harness-engineering, multi-platform]
---

# cc-connect & Aegis — AI Coding Agent 工具双雄

## 快速概览

| 工具 | GitHub | 定位 | 技术栈 |
|------|--------|------|--------|
| **cc-connect** | 7.9k ⭐ | AI Agent 桥接聊天平台 | Go |
| **Aegis** | 113 ⭐ | 架构驱动开发方法包 | Shell |

## cc-connect

**cc-connect** 是将本地 AI Agent 接到聊天平台的桥梁。类比：让本地 AI Agent 获得"微信/飞书/钉钉/Telegram 的手机号"。

**核心价值**：不在电脑前也能远程操控本地 AI 编程助手。

**支持 Agent**：Claude Code、Codex、Cursor Agent、Kimi CLI、OpenCode、Gemini CLI 等 10+
**支持平台**：飞书、钉钉、Telegram、Discord、Slack、微信企业版、个人微信、微博等 11 个

**关键不需要公网 IP**（WebSocket 长连接或长轮询）。

## Aegis

**Aegis** 是在动手前先"三思"的开发纪律。类比：给 AI Agent 装上架构意识的刹车片。

**解决的问题**：
- 任务边界不清就开改
- 声称完成但无证据
- Bug fix 加新逻辑但老代码无人退役
- 长任务中途丢失状态
- 架构漂移事后才发现

**核心机制**：Baseline-first + Evidence before claims + TLREF/DIVE 纪律 + 双轨 Repair/Retirement + 长任务 Checkpoint。

**支持 Host**：Codex ✅、OpenCode ✅、Claude Code（骨架存在）、DeepSeek-TUI（手动安装）。

## 两者关系

两者是**互补**的：

- **cc-connect** = 让 AI Agent 在哪都能被操控（远程控制）
- **Aegis** = 让 AI Agent 怎么操控更可靠（纪律约束）

在 [[Harness Engineering]] 的框架下，cc-connect 属于 **Context/Communication Layer**，Aegis 属于 **Process/Governance Layer**。
