---
title: codexmate
type: entity
created: 2026-05-29
updated: 2026-05-29
sources:
  - https://github.com/SakuraByteCore/codexmate
tags:
  - agent-platforms
  - cli
  - session-management
  - skills-management
---

# codexmate

> 📄 **原文**：[SakuraByteCore/codexmate](https://github.com/SakuraByteCore/codexmate) · 122 ⭐ · Apache-2.0

**Codex Mate** 是本地优先的 AI 编程智能体控制面板，CLI + Web UI 双入口，统一管理 Codex、Claude Code、OpenClaw、Gemini CLI、CodeBuddy 五种智能体的配置、会话与任务编排。

## 核心定位

| 维度 | 描述 |
|------|------|
| **定位** | 多 Agent 统一控制台 / 本地 Agent 桥接器 |
| **语言** | JavaScript（Node.js） |
| **许可** | Apache-2.0 |
| **平台** | Termux · Linux · macOS · Windows |
| **安装** | Homebrew / npm / curl 独立脚本 |

## 架构

```
用户 → CLI / Web UI / MCP Server → HTTP API
                                    ↓
              ┌─────────────────────┴─────────────────────┐
              ↓          ↓          ↓          ↓
          Config     Session    Skills      Tasks
          Engine    Manager    Market     Runner
              ↓          ↓          ↓
        ~/.codex   会话/Usage  Skills 本地存储
        ~/.claude  /Trash
        ~/.openclaw
```

**Core 模块**：
- **Config Engine**：读写各 Agent 本地配置目录（`~/.codex`、`~/.claude`、`~/.openclaw`），统一配置格式
- **Session Manager**：跨工具会话列表、过滤、导出
- **Skills Market**：跨应用 Skills 导入/导出，本地优先
- **Task Runner**：DAG 依赖跟踪的任务编排与日志

**入口**：
- CLI：`codexmate run`（Web UI）
- MCP Server：通过 stdio 暴露本地工具与资源

## 核心功能

| 功能 | 状态 | 说明 |
|------|------|------|
| **Provider 管理** | ✅ | 切换 Codex/Claude/OpenClaw 的 provider 和 model |
| **Live Agent Sync** | ✅ | 实时感知本地配置变更，无需重启 |
| **Session Browser** | ✅ | 跨工具列出会话，支持过滤和导出 |
| **Usage Analytics** | ✅ | 消息趋势可视化、热门项目统计 |
| **Local Skills Market** | ✅ | 跨应用的 Skills 导入/导出，不走云端 |
| **Task Queue** | ✅ | DAG 依赖跟踪的任务执行与日志 |
| **OpenAI Bridge** | ✅ | Codex Responses API → 标准 OpenAI 格式归一化 |
| **Prompt Templates** | ✅ | 可复用提示词插件，支持变量插值 |
| **MCP Integration** | ✅ | stdio 暴露本地工具，符合 MCP 标准 |
| **Auto Update** | ✅ | `codexmate update` 一键升级 |

## 支持的 Agent

| Agent | 安装命令 |
|-------|---------|
| Codex | `npm install -g @openai/codex` |
| Claude Code | `npm install -g @anthropic-ai/claude-code` |
| Gemini CLI | `npm install -g @google/gemini-cli` |
| CodeBuddy | `npm install -g @tencent-ai/codebuddy-code` |
| OpenClaw | 读取 `~/.openclaw` 配置 |

## 与同类工具的对比

| 工具 | 定位 | 多 Agent | Session 跨工具 | Skills 市场 | OpenAI Bridge |
|------|------|---------|--------------|------------|--------------|
| **Codex Mate** | 控制平面 + 桥接器 | ✅ 5 种 | ✅ 统一浏览器 | ✅ 本地优先 | ✅ |
| Multica | 多 Agent 队友平台 | ✅ 11 种 | ❌ | ❌ | ❌ |
| paperclip | 心跳编排平台 | ✅ 适配器层 | ❌ | ❌ | ❌ |
| tessera | Agent 可视化工作区 | ✅ 3 种（并行） | ❌ | ❌ | ❌ |
| ruflo | Claude Code 编排 | ❌ Claude Only | ❌ | ❌ | ❌ |

## 技术细节

- **配置格式**：统一解析多种格式（TOML/JSON5），写入各工具原生配置
- **会话存储**：`~/.codex`、`~/.claude`、`~/.openclaw` 下分散存储，Codex Mate 统一索引
- **无云依赖**：所有数据在本地，无 telemetry，无账号需求
- **MCP stdio**：作为 MCP server 暴露本地工具，其他 MCP Client 可接入
- **版本**：v0.0.37（早期阶段）

## 总结

Codex Mate 的核心价值是**多 Agent 统一控制平面**：不是替代某个 Agent，而是让 Codex/Claude Code/OpenClaw 等在同一界面下共存、切换、共享 Skills。对于同时使用多个 Agent 的开发者（不同项目用不同 Agent），一站式管理配置和会话比分别维护多个工具有效率得多。