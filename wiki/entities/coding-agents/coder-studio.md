---
title: Coder Studio
tags:
  - AI编程工作台
  - 浏览器IDE
  - Supervisor监督循环
  - Claude-Code
  - Codex
  - TypeScript
  - Monorepo
---

# Coder Studio

> **Repo**: [spencerkit/coder-studio](https://github.com/spencerkit/coder-studio)
> **Stars**: 20 | **Language**: TypeScript | **License**: MIT
> **定位**: 浏览器里的 AI 编程工作台，支持 Supervisor 监督循环、多设备切换、跨平台一致体验

## 核心定位

**Deploy once, coding everywhere.** 一个自托管的浏览器端 AI 编程工作台，把终端、文件、Git、AI 会话整合到同一个界面。在桌面、平板、手机之间切换时，上下文完全延续。

与 [paperclip]] 不同，Coder Studio 不做 Agent 编排，而是**围绕单个 Agent 会话**提供更好的可视化和监督能力。Paperclip 适合多租户企业场景，Coder Studio 适合个人开发者长任务监督。

## 架构概览

```
┌─────────────────────────────────────────────────────────────┐
│                    浏览器 (Chrome/Firefox)                   │
│  ┌────────────────────────────────────────────────────────┐  │
│  │   React 18 + Jotai + TanStack Router + Monaco + xterm │  │
│  └────────────────────────────────────────────────────────┘  │
└─────────────────────────────┬───────────────────────────────┘
                              │ WebSocket (ws://127.0.0.1:<port>/ws)
┌─────────────────────────────┴───────────────────────────────┐
│                  Coder Studio Server (Node.js)               │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │ Session Mgr  │  │ File/Git Svc │  │  Storage (SQLite)   │  │
│  │  PTY Host   │  │  chokidar    │  │  better-sqlite3     │  │
│  │  node-pty   │  │   git CLI    │  │                    │  │
│  └──────┬───────┘  └──────────────┘  └────────────────────┘  │
│         │                                                    │
│  ┌──────┴────────────────────────────────────────────────┐  │
│  │          Hooks HTTP Endpoint (POST /internal/hooks/:event)│  │
│  │   bridge script → Claude Code / Codex hooks 回调          │  │
│  └─────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
                              │
                    spawn PTY (claude / codex CLI)
```

**三层核心抽象**（关键设计）：

| 概念 | 职责 |
|---|---|
| **Terminal** | 底层原语：PTY 进程 + ring buffer + xterm 渲染。只管字节流和生命周期，不知道里面跑什么 |
| **Session** | 业务封装：Provider 绑定 + 状态机 + resume_id 追踪 + hook 事件消化。1:1 挂在 agent-kind Terminal 上 |
| **Pane** | UI 层：Agent 工作区的面板节点，递归 split 形成树形布局 |

> **分层原则**：如果移除某段逻辑后 shell 终端仍能正常工作，它属于 Session 层；否则属于 Terminal 层。

## 技术栈

| 层 | 选型 |
|---|---|
| 前端 | React 18, Vite, Jotai, TanStack Router, Monaco Editor, xterm.js |
| 后端 | Fastify, WebSocket (ws), node-pty, chokidar, better-sqlite3 |
| 状态校验 | Zod（前后端共用 schema） |
| 包管理 | pnpm workspaces (Monorepo) |
| 测试 | Vitest + Playwright |

**Monorepo 包结构**：

```
packages/
├── core/          # 纯 TS，前后端共用：protocol/, provider/, domain/, events/
├── providers/    # Claude/Codex 适配层
├── server/       # Node 运行时：ws/, commands/, pty/, session/, hooks/
├── web/          # React SPA
├── hook-bridge/  # 发布时复制到 ~/.coder-studio/hooks/
└── cli/          # 发布入口：bin.js + ESM/CJS bundle
```

## Supervisor 监督循环（Phase 3）

### 核心思想

Supervisor 是一个**自动化评估系统**，周期性地评估 Agent 会话朝目标的进展，并注入指导。目标是"减少机械重复的人工盯守，让长任务执行更稳"。

### 架构

```
┌─────────────────┐
│  Supervisor Mgr  │  — 每工作区/每会话 0 或 1 个
└────────┬────────┘
         │
   ┌─────┴─────┐
   ↓           ↓
┌──────┐   ┌────────┐
│ Sched│   │Evaluator│
└──────┘   └────┬───┘
                │
         ┌──────┴───────┐
         │              │
     ┌───┴─────┐    ┌───┴─────┐
     │Injector │    │ History │
     └─────────┘    └─────────┘
```

**四组件职责**：

| 组件 | 职责 |
|---|---|
| **Scheduler** | 根据周期配置和会话活动触发评估（只看 `session.lifecycle` 事件） |
| **Evaluator** | 构造评估提示（目标 + 最近历史）→ 调用 Anthropic/OpenAI API（不走 Provider CLI）→ 解析结果 |
| **Injector** | 把指导结果通过 `terminal.input` 注入到 PTY，等同用户手动输入 |
| **History** | 评估周期和结果记录 |

### 约束

- **仅 Full 模式 Provider 可启用**（Claude Code 可用，Codex Limited 模式禁用）
- Supervisor 自己的 API 调用使用用户在设置里配置的 key，**独立于 Agent 本身**
- 配置项放在 session 元数据里，不在 Provider 配置里（会话级特性）

### Supervisor vs Paperclip 心跳

两者都是"围绕目标持续推进"的设计，但思路不同：

| | Coder Studio Supervisor | Paperclip 心跳 |
|---|---|---|
| **调度触发** | Scheduler 周期 + 会话活动 | Timer / Assignment / On-demand / Automation |
| **评估方式** | 独立调用 LLM API 评估进展 | 适配器执行 + 结果回传 |
| **注入方式** | 通过 PTY input 注入指导 | 通过心跳响应携带指令 |
| **上下文** | 目标 + 最近历史 | 心跳携带的任意上下文 |
| **Phase** | Phase 3（未实现） | 已实现 |

## Provider 适配层

Claude Code 和 Codex 通过 `ProviderDefinition` 接口接入：

```typescript
// 能力等级决定 Session 层行为
type Capability = "full" | "limited" | "unsupported";

// Full 模式：hooks 回调获取结构化事件（session_start, turn_completed, ...）
// Limited 模式：stdout heuristics 解析（如 "Session ID: abc123"）
// Unsupported 模式：降级 UI，无 Supervisor
```

**Claude Code hooks 集成**：
1. `hooks-template.ts` 生成要写入 `~/.claude/settings.json` 的 hooks 片段
2. `bridge.js` 脚本（无外部依赖）接收 hook 回调 → POST 到 server `/internal/hooks/:event`
3. `Hooks Manager` 解析 payload → 提取 resume_id → 写入 session metadata

## WebSocket 协议设计

三类消息：

| 类型 | 方向 | 示例 |
|---|---|---|
| **Command** | Client → Server | `session.create`, `terminal.input`, `file.write` |
| **Event** | Server → Client | `session.state`, `terminal.output`, `git.status` |
| **Subscribe** | Client → Server | `workspace.<id>.*`, `workspace.<id>.supervisor.<sid>.cycle` |

**单 writer 强制**：Phase 1 只允许一个浏览器 tab 持有写权限。第二个 tab 连接时被拒绝（`another_tab_active`），可通过 `tab.takeover` 命令争夺权限。

## 快速开始

```bash
npm install -g @spencer-kit/coder-studio
coder-studio open   # 浏览器自动打开
```

## 关键设计决策

1. **Server 是唯一真源**：Agent 进程、PTY、文件监听、Git 状态都在 server，前端只是投影
2. **前后端通过单一 WebSocket 多路复用通信**：Command / Event / Subscribe 三类消息
3. **全 TypeScript + Zod**：核心协议在 `@coder-studio/core` 一次定义，前后端共用
4. **Phase 1 不做持久化**：PTY 输出流、日志历史一律内存，server 重启即失
5. **Terminal 与 Session 分层**：Terminal 是底层原语，Session 是业务封装，界限清晰

## 路线图

| Phase | 目标 |
|---|---|
| Phase 1 (MVP) | 多 Agent 并行、编辑保存、Git 基本操作 |
| Phase 2 (Shareable) | 认证、设置、i18n、Provider 全部接好 |
| Phase 3 (Full PRD) | **Supervisor 监督循环**、Worktree 管理、多标签并发 |
| Phase 4 (Quality) | 稳定性、性能优化、持久化方案 |
