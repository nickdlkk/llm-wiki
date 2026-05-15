---
title: Kandev
tags:
  - 多Agent编排
  - Kanban工作流
  - ACP协议
  - Go后端
  - Next.js前端
  - Worktree隔离
  - 自托管
---

# Kandev

> **Repo**: [kdlbs/kandev](https://github.com/kdlbs/kandev)
> **License**: AGPL-3.0
> **定位**: 多Agent并行任务编排 + Kanban工作流 + 集成工作区（编辑器/终端/Git变更），支持自托管

## 核心定位

**Humans stay in control.** 定义任务 → 构建带闸门的 Agentic 工作流 → 审核每次变更 → 决定发布内容。

关键差异于同类平台：
- **Server-first 架构**：非桌面应用，可从任意设备访问（含手机）
- **远程运行时**：Agent 可在远程服务器和 Docker 宿主机上运行，不占用本地资源
- **Review-first**：人类始终处于审核节点，不过度自动化

## 架构概览

```
┌─────────────────────────────────────────────────────────────┐
│                 Web UI (Next.js + Zustand + Dockview)         │
└─────────────────────────────┬───────────────────────────────┘
                              │ WebSocket
┌─────────────────────────────┴───────────────────────────────┐
│              Kandev Binary (Go, 端口 38429)                    │
│  ┌────────────┐  ┌─────────────┐  ┌────────────────────┐   │
│  │ Orchestrator│  │Agent Lifecycle│  │ WebSocket Gateway │   │
│  │   Service   │  │    Manager   │  │                  │   │
│  └─────┬──────┘  └──────┬──────┘  └────────────────────┘   │
│        └────────────────┼───────────────────────────────────┤
│                    In-Memory Event Bus                        │
│  ┌─────────────────────┴─────────────────────────────────┐  │
│  │         agentctl sidecar (随容器/进程启动)              │  │
│  │  HTTP: 文件/Git/进程操作   WebSocket: ACP + Shell I/O   │  │
│  └─────────────────────────────────────────────────────────┘  │
│                    SQLite (kandev.db)                        │
└─────────────────────────────┬───────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ↓               ↓               ↓
        Local Process      Docker Container    Sprites
         (本地 PTY)        (隔离执行)       (远程云端)
              │                  │               │
         ┌────┴────┐        ┌────┴────┐         │
         │ Worktree│        │ Worktree│         │
         └─────────┘        └─────────┘         │
```

## 技术栈

| 层 | 选型 |
|---|---|
| 后端 | Go (统一二进制)，SQLite，WebSocket，Docker SDK |
| 前端 | Next.js，Zustand (状态)，Dockview (面板布局)，Monaco，xterm.js，Tiptap |
| 协议 | ACP (Agent Client Protocol) — JSON-RPC 2.0 over stdin/stdout |
| 包管理 | pnpm workspaces (Monorepo: apps/backend, apps/web, apps/cli, packages/) |

## 后端核心组件

### Orchestrator Service

任务编排中心，管理任务队列调度、Agent 生命周期、事件处理和会话管理。接收任务请求 → 委托给 Lifecycle Manager → 通过 Workflow Engine 处理事件驱动的状态转换。

### Agent Lifecycle Manager

负责启动 Agent 运行时（Local/Docker/Sprites）、管理 agentctl 连接、处理进程启动。配置 `ExecutorRegistry` 支持多种执行环境，追踪 `AgentExecution` 实体。

### agentctl sidecar

运行在 Agent 执行环境内的边车二进制（容器内或本地进程），核心职责：

- **协议翻译**：将 Kandev 内部协议转为 ACP
- **工作区服务**：Shell 管理、Git 追踪
- **暴露接口**：
  - HTTP 端点 → 一次性操作（文件、Git、进程）
  - WebSocket 连接 → ACP 协议更新 + 工作区事件（Shell I/O、Git 事件）

### Worktree Manager

使用 Git worktree 提供工作区隔离，允许多个 Agent 同时在同一个仓库操作而不冲突。

### MCP Server

MCP (Model Context Protocol) Server 向 AI Agent 暴露工具（如 `create_task`、`read_file`），支持 `Task`/`Config`/`External` 三种模式。`lifecycle.Manager` 配置 `MCPHandler` 分发来自 agentctl 实例的 MCP 工具调用。

## ACP 协议

**ACP (Agent Client Protocol)** — 开放协议，JSON-RPC 2.0 over stdin/stdout，是 Agent 与后端通信的核心协议。

**消息流**：
```
Agent stdout → agentctl 捕获解析 → NATS/EventBus → Orchestrator → WebSocket → 前端
```

**核心 ACP 消息类型**：

| 方向 | 消息 | 说明 |
|---|---|---|
| Client → Agent | `session/new` | 创建新会话 |
| Client → Agent | `session/prompt` | 发送提示词 |
| Agent → Client | `session/update` | 进度更新 / 完成通知 |
| Agent → Client | `session/request_permission` | 权限请求（如文件写入） |

Kandev 自动批准 workspace 索引和工具执行权限。

## 支持的 Agent（16款）

通过 ACP 接入：

| Agent | 启动命令 |
|---|---|
| Claude Code | `npx -y @agentclientprotocol/claude-agent-acp` |
| Codex | `npx -y @zed-industries/codex-acp` |
| GitHub Copilot | `npx -y @github/copilot --acp` |
| Gemini CLI | `npx -y @google/gemini-cli --acp` |
| Amp / Auggie / OpenCode / Cursor / Qwen 等 | 各厂商 ACP 适配器 |

也支持 **CLI Passthrough 模式**：在 PTY 终端内直接运行任意 Agent CLI，保留完整 TUI 能力。

## 执行器

| Executor | 说明 |
|---|---|
| **Local Process** | 主机上作为本地进程运行 |
| **Docker** | 隔离 Docker 容器运行 |
| **Sprites** | 通过 sprites.dev 在远程云环境运行 |

所有执行器均使用 Git worktree 做工作区隔离。

## 工作流引擎（5个内置模板）

### Kanban
`Backlog → In Progress → Review → Done`

Agent 在 In Progress 自动启动；完成后进入 Review；发送消息可重新激活。

### Plan & Build
`Todo → Plan → Implementation → Done`

Plan 阶段 Agent 生成结构化计划（含 Mermaid 图），保存后停止供人类审核；可编辑后再继续 Implementation。

### Architecture
`Ideas → Planning → Review → Approved`

专注架构设计阶段，不做实现。产出技术设计和 RFC。

### Feature Dev（最复杂）
`Todo → Spec → Work → Review → QA → PR → CI Fixup → Done`

每个阶段运行新鲜 Agent 上下文的 Turn，上下文精准匹配当前任务：
- **Spec**：探索代码库 → 通过 MCP 保存详细计划 → 人工审核
- **Work**：TDD 循环（失败测试 → 最小代码 → 重构 → 提交）
- **Review**：重置上下文后复审 diff（安全/正确性/性能/质量）
- **QA**：端到端验证，追踪 wiring，跑 happy path，尝试边界值
- **PR**：格式化 → 提交推送 → 填 PR 模板 → 创建 Draft PR
- **CI Fixup**：轮询 CI → 拉取失败日志 → 修 lint/test/type → 推送重试

### PR Review
`Waiting → Review → Done`

自动化代码审查，发现按 BUG / IMPROVEMENT / NITPICK / PERFORMANCE 四类组织，含 `file:line` 引用。

## 关键设计模式

| 模式 | 应用 |
|---|---|
| **Hub-and-Spoke** | Orchestrator 协调分布式 agentctl 实例 |
| **Unified Binary** | 所有核心服务在同一进程，简化部署 |
| **Event-Driven** | 内存 Event Bus 解耦服务 |
| **Provider Pattern** | `Provide(cfg, log) (*impl, cleanup, error)` 依赖注入 |
| **Adapter Pattern** | agentctl 归一化不同 Agent CLI |
| **State Machine** | Workflow Engine 提供类型化状态机评估 |

## 凭证挂载策略

```
只读挂载：
  ~/.ssh          → /root/.ssh
  ~/.gitconfig    → /root/.gitconfig
  ~/.git-credentials → /root/.git-credentials

环境变量：
  GITHUB_TOKEN, GITLAB_TOKEN, GEMINI_API_KEY

读写挂载：
  /tmp/kandev/workspaces/{task_id} → /workspace
```

## 与 Coder Studio / Paperclip 的定位对比

| | Kandev | Coder Studio | Paperclip |
|---|---|---|---|
| **核心** | 多Agent并行 + Review-first 工作流 | 单会话监督 + 跨设备延续 | 多Agent编排 + 企业治理 |
| **审查** | 强制人工审核闸门 | 可选 Supervisor 循环 | 策略/预算控制 |
| **工作流** | 5个内置模板 + 自定义 | 无（Phase 1 不做） | 多租户工作流 |
| **协议** | ACP (JSON-RPC 2.0) | Provider hooks | 适配器层 |
| **运行时** | Local / Docker / Sprites | Local PTY | Server Adapter |
| **License** | AGPL-3.0 | MIT | 私有 |

## 快速开始

```bash
brew install kdlbs/kandev/kandev
kandev

# 或 npx
npx kandev@latest
```

数据目录：`~/.kandev/`
