---
title: Routa
type: entity
created: 2026-05-17
updated: 2026-05-17
sources:
  - https://github.com/phodal/routa
tags:
  - Multi-Agent-Orchestration
  - Agent-Platforms
  - Workspace-First
  - Kanban-Board
  - ACP
  - MCP
  - Phodal
---

# Routa

**Workspace-first multi-agent coordination platform for software delivery.** 将目标、任务、会话、轨迹、证据和审查状态在看板（Kanban）上可视化，而非埋藏在单一聊天线程中。

- **905 ⭐**（GitHub: phodal/routa）
- 双后端架构（Next.js/TypeScript + Tauri/Rust）
- 核心协议：ACP、MCP、A2A、AG-UI

## 核心定位

不同于其他多 Agent 协调平台，Routa 的核心差异在于：

1. **看板作为协调总线**：每个泳道（lane）背后是专属的 Specialist prompt + 证据契约，下游泳道比上游更严格
2. **Review Gate 架构**：三重门控（Harness Monitor / Entrix Fitness / Gate Specialist）防止 AI 幻觉
3. **协议导向编排**：ACP、MCP、A2A、AG-UI 协议栈，桥接自然语言意图与可执行代码
4. **双后端语义一致**：Web（Next.js + PostgreSQL）和桌面（Tauri/Rust + SQLite）运行相同领域语义，共享 `api-contract.yaml` 定义

## 架构图

```
用户输入（目标描述）
       ↓
   Workspace
       ↓
 Backlog → Todo → Dev → Review → Done
  ↑                            ↓
  └────── Blocked ←────────────┘
       ↑
 Backlog Refiner / Todo Orchestrator / Dev Crafter / Review Guard / Done Reporter / Blocked Resolver
```

## 六泳道 Specialist 系统

| 泳道 | Specialist | 职责 | 关键约束 |
|------|-----------|------|---------|
| Backlog | Backlog Refiner | 澄清范围，生成规范 YAML story | 不写代码，card 必须可独立执行才进 Todo |
| Todo | Todo Orchestrator | 验证上游输出，创建执行简报 | 拒绝格式错误/模糊卡片 |
| Dev | Dev Crafter | 实现范围变更，运行验证，提交代码 | 仅实现 scoped change，保持 git clean |
| Review | Review Guard | 独立验证每个验收标准 | 拒绝证据缺失、范围蔓延、脏 git 状态 |
| Done | Done Reporter | 记录完成摘要 | 视为终态，不再推进 |
| Blocked | Blocked Resolver | 分类阻塞、根因分析、路由决策 | 仅在有明确下一步时路由回上游 |

## Review Gate 架构

三层递进决策路径（见 `docs/review-gate.svg`）：

```
                         Review Gate
                              │
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
   Harness Monitor    Entrix Fitness       Gate Specialist
   （发生了什么）      （应该是什么）       （卡能移动吗）
          │                   │                   │
   轨迹/文件/命令       硬门禁/策略检查      验收标准/Verdict
   /git状态/归属        /lint/测试/文件预算    /路由决策
```

- **Harness Monitor**：TUI + Web 面板，回答"发生了什么"——暴露轨迹、变更文件、命令、git 状态、归属
- **Entrix Fitness**（Rust 引擎）：回答"应该是什么"——强制 lint/类型检查/测试/架构规则/文件预算
- **Gate Specialist**（Review Guard）：回答"卡能否移动"——逐条验证验收标准，给出 APPROVED / REJECT / ESCALATE verdict

## 双后端架构

| 维度 | Web 后端 | 桌面后端 |
|------|---------|---------|
| 运行时 | Next.js（TypeScript 5.9） | Tauri + Axum（Rust） |
| 数据持久 | PostgreSQL（生产）/SQLite（开发） | SQLite + 本地文件系统 |
| 核心引擎 | AcpProcessManager（TS） | AcpManager（Rust） |
| 特性 | API routes，HTTP/SSE | PTY 支持，进程隔离 |

**共享契约**：`api-contract.yaml` 定义跨运行时一致的行为语义。

## 核心协议栈

| 协议 | 全称 | 职责 | 实现 |
|------|------|------|------|
| **ACP** | Agent Client Protocol | 管理 Agent 进程生命周期（spawn/prompt/cancel） | `agent-client-protocol` crate |
| **MCP** | Model Context Protocol | 向 Agent 暴露工具（create_task/delegate_task 等） | `rmcp` crate |
| **A2A** | Agent-to-Agent | 跨平台 Agent 消息传递与联邦 | 原生支持 |
| **AG-UI** | Agent-to-UI | 事件适配器，流式 UI 更新和仪表盘生成 | 原生支持 |

## ACP 进程管理

`AcpProcessManager`（TS 单例）/ `AcpManager`（Rust）负责：

- 维护多类型进程注册表（标准 ACP / Claude Code / OpenCode SDK）
- 统一路由创建会话，基于 `presetId` 决定进程类型
- 标准化握手机制（initialize → session/new → session/set_mode）
- Claude Code 使用 `stream-json` 格式 → 翻译为 ACP 兼容的 `session/update` 通知
- MCP 配置集成：启动时写入配置文件并注入 CLI 参数

## 技术栈

- **TypeScript**（Next.js 16.2）：Web 前端 + API routes
- **Rust**（Axum）：桌面后端核心服务
- **Tauri**：桌面应用 shell
- **PostgreSQL / SQLite**：按运行时选择持久化层
- **Drizzle ORM**：关系型数据库抽象

## 卡片产物随工作推进增长

同一张卡在推进过程中累积产物：

```
Backlog → canonical story YAML（含 INVEST 检查）
Todo    → execution brief + 依赖计划 + 风险笔记
Dev     → Dev Evidence（变更文件列表 + 测试结果 + per-AC 验证）
Review  → Review Findings（verdict + per-AC 状态 + 问题清单）
Done    → Completion Summary（交付物 + 关键证据 + 完成日期）
```

每层产物构成下一层的输入验证契约。