---
title: Vibe-Remote
type: entity
entity_type: tool
created: 2026-05-16
updated: 2026-05-16
sources: [cyhhao/vibe-remote-github]
tags: [ai-agent, python, remote-control, slack, discord, telegram, wechat, lark, claude-code, opencode, codex]
---

# Vibe-Remote

**Vibe-Remote** 是本地优先的 AI Agent 远程控制层（Python ≥3.10, MIT License），通过 Slack、Discord、Telegram、WeChat、Lark/飞书控制本地运行的 AI 编程助手（Claude Code、OpenCode、Codex），实现"从任何设备远程操控本地 AI 编程助手"。

> **No laptop. No IDE. Just vibes.**

## Key Facts

- **作者**: cyhhao
- **GitHub**: [cyhhao/vibe-remote](https://github.com/cyhhao/vibe-remote) — Python ≥3.10, MIT
- **License**: MIT
- **技术栈**: Python (后端/CLI) + React TypeScript (WebUI)
- **安装方式**: `curl -fsSL https://avibe.bot/install.sh | bash && vibe`
- **最新版本**: v2 (2026-05)

## 核心理念

Vibe-Remote 的核心差异化定位是 **remote control layer**（远程控制层），而非 agent 框架或托管编程 VM。它是将你本地运行的编码 agent 接入聊天应用的桥接层：

- **不造 agent**：Claude Code、OpenCode、Codex 本身就是 agent，Vibe-Remote 只是远程遥控器
- **本地优先**：你的数据永远不会离开你的机器
- **极简传输层**：thin transport layer，零 LLM 开销

## 与 OpenClaw 的对比

| | Vibe-Remote | OpenClaw |
|---|---|---|
| **Setup** | 一条命令 + Web 向导，2 分钟搞定 | Gateway + channels + JSON config，需要一下午 |
| **安全性** | 本地优先，Socket Mode / WebSocket only，无公网端点，无入站端口 | Gateway 暴露端口，更多移动部件，更多攻击面 |
| **Token 成本** | 薄传输层——在 IM 和 agent 之间 relay 消息，零 LLM 开销 | 每条消息都携带长长的系统上下文（agent persona、IM 工具、编排管道），token 在实际任务开始前就烧掉了 |

## 核心功能

### 支持的聊天平台

| 平台 | 支持情况 |
|------|:--------:|
| Slack | ✅ 完整支持 |
| Discord | ✅ 完整支持 |
| Telegram | ✅ 完整支持 |
| WeChat | ✅ 完整支持 |
| 飞书 (Lark/Feishu) | ✅ 完整支持 |

### 支持的 Agent

| Agent | 说明 |
|:------|------|
| **Claude Code** | Deep reasoning，复杂重构 |
| **OpenCode** | 快速、可扩展，社区最爱 |
| **Codex** | OpenAI 的编程模型 |

### 核心特性

- **Thread = Session**：每个 Slack/Discord/Telegram/WeChat/Lark 聊天范围是一个隔离的工作区。打开 5 个聊天，运行 5 个并行任务，context 相互独立。
- **即时通知**：AI 完成后立即通知，像派任务给员工一样——委托、去做别的事、完成后回来。
- **任意位置继续**：笔记本合上，在路上，生产环境仍需要你。Vibe-Remote 让你从当前工作目录重新打开真实的 agent session。
- **交互式提示**：agent 需要输入（文件选择、确认、选项）时，聊天 app 弹出按钮或模态框，完全 CLI 交互性，零终端需求。
- **定时任务 & 异步钩子**：让 agent 安排后续工作、cron 重复提示、长任务完成后排队一次性异步 follow-up。
- **每个线程 = 隔离 session**：启动多个线程并行任务，context 隔离。
- **按平台路由**：不同项目、不同 agent（`#frontend → OpenCode`、`#backend → Claude Code`、`#prototypes → Codex`）。

## 架构

```
┌──────────────┐             ┌──────────────┐             ┌──────────────┐
│     You      │   Slack     │              │   stdio     │  Claude Code │
│  (anywhere)  │   Discord   │ Vibe-Remote  │ ──────────▶ │  OpenCode    │
│              │   Telegram  │ (your host)  │ ◀────────── │  Codex       │
│              │   WeChat    │              │             │              │
│              │   Lark      │              │             │              │
└──────────────┘             └──────────────┘             └──────────────┘
```

1. **你在** Slack/Discord/Telegram/WeChat/Lark 中输入：*"Add dark mode to the settings page"*
2. **Vibe-Remote** 路由到你配置的 agent
3. **Agent** 读取你的代码库、写代码、流式返回
4. **你在** 聊天 app 中 review，thread 中迭代

**你的数据永远不会离开你的机器**。Vibe-Remote 本地运行，通过 Slack Socket Mode、Discord Gateway、Telegram Bot API long polling、WeChat polling 或 Lark WebSocket 连接。

## 安全模型

- **Local-first** — Vibe-Remote 运行在你的机器上
- **Socket Mode / WebSocket** — 无公网 URL，无 webhook
- **你的 tokens** — 存储在 `~/.vibe_remote/`，从不上传
- **你的数据** — 留在你的磁盘，只发送到你选择的 AI provider

## 安装

```bash
curl -fsSL https://avibe.bot/install.sh | bash && vibe
```

浏览器打开 → 跟随向导 → 完成。

需要从另一台设备或远程服务器打开 Web UI？

```bash
vibe remote
```

## 远程访问（avibe.bot tunnel）

```bash
vibe remote
```

一条命令，你的本地 Web UI 变成从任何浏览器可达——通过安全的 avibe.bot tunnel。

- 🌍 **你自己的 `you-app.avibe.bot`** — 30 秒登录，你的终身 slug
- 🔒 **每次 join 都 fail-closed** — 认证、路由、host 检查默认"拒绝"
- 📱 **移动端适配 UI** — 拇指友好的布局，为借用屏幕设计
- ⏱ **24 小时 session** — Cookie 自动续期，不会突然登出

无需 VPN、无需端口转发、无需"我的 IP 是什么"。**你的数据面留在你的机器上**；avibe.bot 只是控制面握手。

## CLI

```bash
vibe          # 启动一切
vibe status   # 检查运行状态
vibe stop     # 停止
vibe doctor   # 诊断问题
vibe task     # 创建和管理定时任务
vibe hook     # 排队一次性异步发送
```

## 命令

| 在聊天中 | 功能 |
|----------|------|
| `@Vibe-Remote /start` | 打开控制面板 |
| `/stop` | 终止当前 session |
| 直接输入 | 和你的 agent 对话 |
| Thread 回复 | 继续对话 |

## Agent 切换

对话中需要切换不同 agent？直接前缀：

```
Plan: Design a new caching layer for the API
```

键入 `AgentName:` 消息立即路由到该 agent。

## 前置要求

至少安装一个编码 agent：

```bash
# OpenCode (推荐)
curl -fsSL https://opencode.ai/install | bash

# Claude Code
npm install -g @anthropic-ai/claude-code

# Codex
npm install -g @openai/codex
```

## 相关概念

- [[cc-connect]] — 另一个 AI Coding Agent 桥接工具
- [[OpenBee]] — 类似的数字员工方案
- [[NanoBot]] — 超轻量级 AI Agent 框架

## Sources

- [[cyhhao/vibe-remote-github]] — 官方 GitHub 仓库
