# Vibe Kanban

> ⚠️ **项目状态：正在关闭（Sun-setting）**
> Vibe Kanban 已宣布停止运营。

## 概述

**Vibe Kanban** 是 Bloop AI 开发的一个 AI 代码智能体协作平台，核心理念是：**在 AI 编程时代，最高效的团队不是拥有最快代码编辑器的人，而是规划、审查速度最快的团队**。

用看板规划任务，AI 代码智能体在 Workspace 中执行，团队在 UI 中 Review 代码 diff。

**官网**: [vibekanban.com](https://www.vibekanban.com)
**GitHub**: [BloopAI/vibe-kanban](https://github.com/BloopAI/vibe-kanban)

## 核心功能

- **看板任务规划** — 创建、优先级排序、分配看板卡片
- **Workspace 执行环境** — 每个 Workspace 给智能体一个独立 Git 分支、终端、开发服务器
- **Diff Review** — 在 UI 中查看 AI 修改的代码 diff，直接 inline 评论反馈给智能体
- **内置浏览器预览** — 集成 DevTools、Inspect 模式、设备模拟
- **10+ 智能体支持** — Claude Code、Codex、Gemini CLI、GitHub Copilot、Amp、Cursor、OpenCode、Droid、CCR、Qwen Code
- **PR 创建与合并** — 自动生成 PR 描述，推送到 GitHub

## 技术架构

Vibe Kanban 是一个 **Rust (后端) + TypeScript/React (前端)** 的混合 monorepo。

### 后端：Rust Workspace

31 个 Rust crate，Edition 2024：

```
Cargo Workspace (31 crates)
├── server              # Axum HTTP/WS 主服务入口
├── executors           # AI 智能体执行器（10+ 种协议支持）
├── workspace-manager   # Workspace 生命周期管理
├── worktree-manager    # Git worktree 创建/切换/清理
├── db                  # SQLite 数据库抽象（sqlx）
├── mcp                 # MCP (Model Context Protocol) 服务器
├── relay-tunnel-core   # 远程 Tunnel 核心（Yamux 多路复用）
├── relay-ws            # WebSocket relay 桥接
├── ws-bridge           # WebSocket 协议转换
├── git / git-host      # Git 操作 + GitHub/GitLab host 集成
├── preview-proxy       # 内置浏览器预览请求代理
├── embedded-ssh        # 嵌入式 SSH 客户端
├── desktop-bridge      # Tauri 桌面桥接
├── api-types           # 共享 API 类型定义（TypeScript 绑定）
└── relay-*             # 多种 relay 协议实现（WebRTC、Tunnel、Control）
```

**关键依赖**：
- `axum 0.8.4` — HTTP server + WebSocket
- `tokio` — 异步运行时
- `sqlx 0.8 + SQLite` — 数据库
- `git2` — Git 操作
- `tokio-yamux` — Tunnel 多路复用
- `rmcp 1.2` — MCP 协议实现
- `rustls 0.23` (aws_lc_rs) — TLS
- `sentry` — 错误追踪
- `agent-client-protocol 0.8` — ACP 智能体通信协议
- `codex-protocol` — OpenAI Codex 协议

### 前端：TypeScript Workspace

```
@vibe/local-web   # 本地桌面版 Web UI (Electron/Tauri)
@vibe/remote-web  # 远程 Web UI
@vibe/web-core    # 核心 Web 组件库
@vibe/ui          # UI 组件库
```

前端使用 Vite 构建，后端默认端口 `3000`，开发环境前端 `localhost:3000`，后端 `localhost:3001`。

### 智能体协议支持

`executors` crate 是多智能体执行引擎，支持两种协议：

| 协议 | 来源 | 用途 |
|------|------|------|
| **ACP** (Agent Client Protocol) | OpenClaw | JSON-RPC 2.0 over stdin/stdout |
| **codex-protocol** | OpenAI Codex | Codex 专用通信协议 |

支持通过**标准 I/O (stdio)** 接入的智能体：Claude Code、Codex、OpenCode、Droid、CCR 等（通过 `agent-client-protocol`）。

### 远程访问：Relay Tunnel

```
Local Client  →  Relay Tunnel (Yamux)  →  Remote Server
              ←  WebSocket Bridge  ←
```

- `relay-tunnel-core`: 基于 Yamux 的多路复用隧道
- `relay-ws`: WebSocket 传输层
- `ws-bridge`: WebSocket ↔ 其他协议桥接
- 支持通过 SSH 连接到远程服务器上的 Workspace

### 部署模式

1. **本地桌面应用** — `npx vibe-kanban` 启动本地全栈（SQLite），通过 Tauri 封装
2. **Docker 自托管** — 官方提供 Docker 部署指南，支持反向代理
3. **远程开发** — 通过 SSH 连接到远程服务器，在远程 Workspace 中执行智能体

### 数据库

SQLite (`sqlx`)，macOS 开发环境自动从 `dev_assets_seed` 目录复制空数据库。

## 启动方式

```bash
# 一键启动（NPX）
npx vibe-kanban

# 开发模式
pnpm run dev
```

**环境变量**：

| 变量 | 默认 | 说明 |
|------|------|------|
| `PORT` | 自动 | 服务器端口（后端 = PORT+1） |
| `HOST` | `127.0.0.1` | 服务器绑定地址 |
| `MCP_PORT` | 同 BACKEND_PORT | MCP 服务器端口 |
| `VK_ALLOWED_ORIGINS` | — | 允许的跨域来源（反向代理必填） |
| `VK_TUNNEL` | — | 启用 relay tunnel 模式 |
| `DISABLE_WORKTREE_CLEANUP` | — | 禁用 Git worktree 自动清理 |

## 开发

**前置要求**：
- Rust (stable)
- Node.js >= 20
- pnpm >= 8

**初始化**：
```bash
cargo install cargo-watch sqlx-cli
pnpm i
pnpm run dev
```

**构建 Web UI**：
```bash
cd packages/local-web && pnpm run build
```

## 技术亮点

1. **Git Worktree 隔离** — 每个 Workspace 使用独立 Git worktree，分支间完全隔离，无需克隆多个 repo
2. **Rust + TypeScript 混合 monorepo** — 后端 Rust 提供高性能和内存安全，前端 TypeScript/React 提供灵活 UI
3. **MCP 原生支持** — 内置 MCP 服务器，智能体可通过 MCP 协议调用 Vibe Kanban 能力
4. **多协议执行器抽象** — `enum_dispatch` 模式让 executors 支持 ACP、Codex 等多种协议无缝切换
5. **Yamux 多路复用** — relay tunnel 在单连接上多路复用多个会话，降低远程开发延迟
6. **Rust Edition 2024** — 使用最新 Rust edition

## 相关项目

- [Vibe Kanban (@vibekanban)](https://vibekanban.com) — 官方平台
- [Bloop](https://github.com/BloopAI/bloop) — Bloop 公司的核心代码搜索产品
