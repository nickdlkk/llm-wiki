---
title: ClawPort (clawport-ui)
description: Open-source AI agent command center for Claude Code agent teams — Built on OpenClaw，提供org chart、chat、kanban、cron、cost tracking
github: https://github.com/JohnRiceML/clawport-ui
stars: 874
tags: [openclaw, claude-code, dashboard, agent-management, multi-agent, open-source]
created: 2026-05-16
---

# ClawPort (clawport-ui)

## 概述

**ClawPort** (npm包名: `clawport-ui`) 是一个开源的AI Agent命令中心，用于管理、监控和直接与 [OpenClaw](https://openclaw.ai) AI Agent团队对话。连接本地OpenClaw网关，提供组织图谱、带视觉/语音的Agent直接聊天、看板板、Cron监控、成本跟踪、活动控制台（实时日志流）和记忆浏览器。

**GitHub**: [JohnRiceML/clawport-ui](https://github.com/JohnRiceML/clawport-ui)  
**官网**: [clawport.dev](https://clawport.dev)  
**Language**: TypeScript  
**License**: MIT  
**Stars**: 874 ⭐  
**npm**: [clawport-ui](https://www.npmjs.com/package/clawport-ui)  

## 核心功能

### 界面截图

| 功能 | 截图 |
|------|------|
| Org Map | 组织图谱，展示Agent层级 |
| Chat | 流式文本、视觉、语音、文件附件 |
| Kanban Board | 跨Agent的拖放任务板 |
| Pipelines | DAG可视化 + 健康检查 |
| Cron Schedule | 周热图 + 作业管理 |
| Activity | 历史日志浏览器 + JSON扩展 |
| Live Logs | 实时流式日志窗口 |
| Costs | Token使用、异常、优化洞察 |
| Memory | 团队记忆浏览器 + Markdown渲染 |

## 快速开始

### 1. 安装 OpenClaw

```bash
# 安装 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# 运行引导向导
openclaw onboard --install-daemon

# 验证网关状态
openclaw gateway status
```

### 2. 安装 ClawPort

```bash
npm install -g clawport-ui
```

### 3. 连接和启动

```bash
# 自动检测OpenClaw配置
clawport setup

# 启动仪表盘
clawport dev
```

访问 [http://localhost:3000](http://localhost:3000)

## 技术架构

```
ClawPort
├── OpenClaw Gateway (本地)
├── Next.js 前端 (TypeScript)
├── Org Chart (Agent组织图)
├── Chat Engine (视觉/语音/文本)
├── Kanban Board (任务管理)
├── Cron Monitor (定时任务)
├── Cost Tracker (成本分析)
├── Activity Console (日志)
└── Memory Browser (记忆)
```

## 与其他工具对比

| 工具 | 定位 | 核心功能 |
|------|------|----------|
| ClawPort | OpenClaw可视化 | Dashboard + Chat |
| Vibe Kanban | AI代码执行 | 看板 + Git worktree |
| AgentVerse | 多Agent讨论 | 团队聊天 + 角色 |
| Aperant | 自主编码 | 端到端自动化 |

## 相关链接

- [GitHub Repository](https://github.com/JohnRiceML/clawport-ui)
- [官网](https://clawport.dev)
- [npm包](https://www.npmjs.com/package/clawport-ui)
- [Setup Guide](SETUP.md)
- [API Docs](docs/API.md)
