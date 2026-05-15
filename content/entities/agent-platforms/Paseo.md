---
title: Paseo
type: entity
created: 2026-05-15
updated: 2026-05-15
sources:
  - https://github.com/getpaseo/paseo
  - https://paseo.sh
tags:
  - agent-orchestration
  - multi-agent
  - remote-control
  - cross-platform
  - voice-control
  - Claude-Code
  - Codex
  - OpenCode
  - TypeScript
---

# Paseo

> One interface for all your Claude Code, Codex and OpenCode agents — orchestrate coding agents remotely from your phone, desktop and CLI.

**GitHub**: [getpaseo/paseo](https://github.com/getpaseo/paseo) · ★6,176 · AGPL-3.0 · TypeScript

## 核心定位

Paseo 是一个**跨设备 Agent 编排平台**，通过本地 daemon + 多端客户端（iOS/Android/Web/Desktop/CLI）实现对 Claude Code、Codex、OpenCode 的远程控制和并行编排。

## 核心架构

```
Paseo Daemon（本地服务器）
├── WebSocket API（多客户端连接）
├── MCP Server
└── Agent Processes（Claude Code / Codex / OpenCode）

客户端：CLI / Web / Electron Desktop / iOS / Android
```

## 核心特性

### 多 Provider 支持

| Provider | 模型 |
|----------|------|
| Claude Code | Claude 系列 |
| Codex | GPT 系列 |
| OpenCode | 开源模型 |

### 跨设备同步

- iOS、Android 移动端
- Electron 桌面应用
- Web 浏览器
- CLI 脚本化操作

### Skills 系统

| Skill | 功能 |
|-------|------|
| `/paseo-handoff` | Agent 之间移交工作 |
| `/paseo-loop` | 循环 Agent 对抗验收标准 |
| `/paseo-advisor` | 启动顾问 Agent |
| `/paseo-committee` | 组建委员会做根因分析 |

### 隐私优先

- ❌ 无遥测
- ❌ 无追踪
- ❌ 无强制登录

## CLI 用法

```bash
# 运行 Agent
paseo run --provider claude/opus-4.6 "implement user authentication"
paseo ls                           # 列出运行中的 Agent
paseo attach abc123                # 流式查看实时输出
paseo send abc123 "also add tests" # 发送跟进任务

# 远程 daemon
paseo --host workstation.local:6767 run "run the full test suite"
```

## 安装

```bash
# CLI
npm install -g @getpaseo/cli
paseo

# Desktop app: paseo.sh/download
```

## 相关页面

- [[paperclip]] — 心跳驱动的 Agent 编排平台
- [[Harness Engineering]] — Agent 可靠工作工程化方法论
