---
title: OpenHuman
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/tinyhumansai/openhuman
tags:
  - AI Agent
  - Agent平台
  - 本地记忆
  - Tauri
  - Rust
---

# OpenHuman

**开源 agentic 个人助手**。Tauri 桌面壳 + React 前端 + Rust 核心引擎，深度集成用户数字生活，严格本地隐私优先。

## 核心定位

自托管的"AI harness"，定位介于 Hermes Agent（自托管工具）+ OpenClaw（插件生态）+ Claude Cowork（商业产品）之间。核心差异：

- **118+ OAuth 一键集成**：Gmail、Notion、GitHub、Slack、Stripe、Calendar、Drive、Linear、Jira 等，20 分钟自动同步数据到本地
- **Memory Tree + Obsidian Vault**：SQLite 本地存储，分层摘要树，内容以 `.md` 文件同步到 Obsidian 兼容知识库（Karpathy 风格）
- **TokenJuice 压缩**：工具调用、邮件、搜索结果统一压缩，最多减少 80% token 消耗
- **内置 Model Routing**：自动分发任务到合适的 LLM（推理型/快速型/视觉型），单订阅
- **可选 Ollama 本地 AI**：对端侧工作负载支持本地推理

## 架构概览

### 桌面壳（Tauri + CEF）
- Tauri 驱动的轻量桌面应用
- CEF（Chromium Embedded Framework）WebView
- Webview 账户系统 + CDP Scanner
- 全局快捷键、语音听写、桌面 Overlay

### Rust 核心引擎
- JSON-RPC Server + Controller Registry
- 配置系统（schema/types/load）
- 凭证加密（本地加密存储）
- 事件总线 + Socket.IO 通知

### Agent 系统
- Turn 执行模型
- Agent 定义 + 子 Agent 委托
- 上下文管理与压缩
- Triage 路由 + Provider 选择

### Memory & Neocortex
- **Memory Tree 三层架构**：Source Tree → Global Tree → Topic Tree
- Embeddings 检索 + 评分系统
- 用户画像学习（Learning & User Profile）
- **Auto-fetch**：每 20 分钟主动拉取各集成数据入树
- **Obsidian Vault 同步**：所有内容以 ≤3k-token Markdown chunk 存储为本地 `.md` 文件

### Skills 运行时
- QuickJS 技能运行时
- Composio 集成桥接
- 内置工具注册（文件系统、git、lint、test、grep、搜索、爬虫）
- Messaging Channels（跨平台消息）

### 本地 AI（Ollama）
- Ollama Service 管理 + Model Tiers
- STT（语音识别）/ TTS（ ElevenLabs 输出）/ 语音听写
- 屏幕理解 + Accessibility

### 前端（React）
- App Shell + 路由 + 状态管理
- Onboarding 流程
- Chat 界面 + 会话运行时
- Skills / Home / Intelligence 页面
- 设置 / 通知 / 计费
- 隐私与错误上报

### 自动化
- Webhooks
- Cron 调度

## 技术栈

| 层级 | 技术 |
|------|------|
| 桌面壳 | Tauri + CEF |
| 前端 | React |
| 核心引擎 | Rust |
| 技能运行时 | QuickJS |
| 本地 AI | Ollama |
| 存储 | SQLite |
| 通信 | JSON-RPC + Socket.IO |
| 集成 | Composio + 118+ OAuth |

## 突出特性

1. **Auto-fetch**：20 分钟一次主动拉取，用户"早上醒来时 agent 已具备前一天的上下文"
2. **TokenJuice**：HTML→Markdown、长 URL 缩短、非 ASCII 字符清理，统一压缩层
3. **Obsidian Wiki 同步**：Memory Tree 内容实时落地为 Obsidian `.md` 文件，用户可直接浏览编辑
4. **agentmemory 后端**：可选对接 `agentmemory`（支持 Claude Code、Cursor、Codex、OpenCode 共用同一记忆存储）
5. **桌面吉祥物**：带脸部的桌面助手，可加入 Google Meet 作为真实参与者，有嘴唇同步动画

## 与同类对比

|  | Claude Cowork | OpenClaw | Hermes Agent | **OpenHuman** |
|--|--------------|----------|-------------|--------------|
| 开源 | 🚫 闭源 | ✅ MIT | ✅ MIT | ✅ GNU |
| 易用性 | ✅ 桌面+CLI | ⚠️ Terminal-first | ⚠️ Terminal-first | ✅ Clean UI，分钟级上手 |
| 费用 | ⚠️ 订阅+附加 | ⚠️ BYO 模型 | ⚠️ BYO 模型 | ✅ 单一订阅+TokenJuice |
| 记忆 | ✅ Chat 范围 | ⚠️ 依赖插件 | ✅ 自学习 | 🚀 Memory Tree + Obsidian |
| 集成 | ⚠️ 少数 | ⚠️ 自建 | ⚠️ 自建 | 🚀 118+ OAuth |
| Auto-fetch | 🚫 无 | 🚫 无 | 🚫 无 | ✅ 20 分钟轮询 |
| 本地模型 | 🚫 | 🚫 | ✅ | ✅ Ollama |

## 部署

```bash
# macOS / Linux x64
curl -fsSL https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.sh | bash

# Windows
irm https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.ps1 | iex
```

## 开发

前置：Git, Node.js 24+, pnpm 10.10.0, Rust 1.93.0, CMake, Ninja, ripgrep

```bash
git clone --recursive
pnpm install
pnpm dev                    # 仅 Web UI
pnpm --filter openhuman-app dev:app  # 桌面壳
```

## 相关链接

- [GitHub](https://github.com/tinyhumansai/openhuman)
- [官网](https://tinyhumans.ai/openhuman)
- [文档](https://tinyhumans.gitbook.io/openhuman/)
- [Discord](https://discord.tinyhumans.ai/)
