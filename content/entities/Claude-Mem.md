---
title: Claude-Mem
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/thedotmack/claude-mem
tags:
  - memory
  - claude-code
  - plugin
---

# Claude-Mem

**Claude-Mem**（GitHub: [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)）是专为 [Claude Code](https://claude.ai/claude-code) 设计的**持久记忆压缩系统**。不是独立的记忆产品，而是 Claude Code 的插件/扩展。

> ⚠️ 注意：不要将 Claude-Mem 与 [[Supermemory]]（mem0）混淆——两者都是 AI 记忆工具，但定位不同：Claude-Mem 是 Claude Code 的本地插件，Supermemory 是通用多 Agent 记忆平台。

## 核心功能

- **自动记忆捕捉**: Claude Code 的每次对话自动压缩并存储
- **上下文压缩**: 将长对话压缩为结构化记忆，节省上下文窗口
- **跨会话持久化**: 记忆在会话之间保持，供未来对话使用
- **增量学习**: 每一次新对话都会更新和补充已有记忆

## 工作原理

```
Claude Code 对话
    ↓
自动提取关键信息
    ↓
压缩为结构化记忆（Summaries / Facts）
    ↓
持久化存储（本地文件或云端）
    ↓
下次对话时注入相关记忆
```

## 与其他记忆系统的区别

| 特性 | Claude-Mem | Supermemory | Honcho |
|------|------------|-------------|--------|
| 目标 Agent | Claude Code 专用 | 多 Agent | Hermes |
| 记忆来源 | 仅 Claude Code | 多数据源 | 对话提取 |
| 部署方式 | 本地插件 | 云优先 | 自托管/云 |
| 生态规模 | 小（单一工具）| 大（多 SDK/集成）| 中 |

## 版本与许可

- 当前版本: 6.5.0
- 许可: Apache 2.0
- 最低 Node.js: 18.0.0

## 局限性

- 仅适用于 Claude Code，对其他 Agent 无效
- 功能相对单一（vs Supermemory 的完整生态）
- 无官方 SDK 或 API 供第三方集成
- 记忆格式封闭，不可与其他系统互操作

## 相关项目

- [[Memory Systems]] — 记忆系统总体对比
- [[Supermemory]] — 更通用的多 Agent 记忆平台
- [[Honcho]] — Hermes Agent 的专用记忆层
