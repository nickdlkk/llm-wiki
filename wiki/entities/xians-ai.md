# Xians.ai

> Enterprise-grade Agent Development Kit — 构建、部署、编排 AI Agent 的开源平台

## 概述

Xians.ai 是由 [XiansAiPlatform](https://github.com/XiansAiPlatform) 开发的开源 Agent 平台，定位为"Enterprise-grade Agent Development Kit"。核心特性是 **Temporal.io 工作流引擎 + LLM-agnostic 多模型支持 + 多租户 + 多渠道通信**。

官网：[xians.ai](https://xians.ai) | 文档：[xiansaiplatform.github.io/XiansAi.PublicDocs](https://xiansaiplatform.github.io/XiansAi.PublicDocs/) | Docker Hub：[99xio](https://hub.docker.com/u/99xio)

## 架构设计

### 核心组件

```
┌─────────────────────────────────────────────────────┐
│                   Xians.ai Platform                 │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌──────────────┐   ┌──────────────────────────┐ │
│  │ Xians.UI     │   │   Agent Studio (Next.js)  │ │
│  │ (React+MUI)  │   │   Nordic Design System   │ │
│  │ Portals/管理 │   │   Agent 可视化编排        │ │
│  └──────┬───────┘   └──────────┬───────────────┘ │
│         │                        │                  │
│         └────────┬────────────────┘               │
│                  │ WebSocket / REST / SSE          │
│         ┌────────▼────────┐                        │
│         │ Xians.Server    │ (.NET 9 / ASP.NET)     │
│         │ ─────────────  │                        │
│         │ WebApi (Client) │ ← 外部客户端 SDK       │
│         │ LibApi (Server) │ ← Agent Lib 通信      │
│         │ UserApi (User) │ ← 用户对话             │
│         └────┬───────┬───┘                        │
│              │       │                             │
│    ┌─────────▼───┐ ┌─▼───────────┐               │
│    │  Temporal.io │ │  MongoDB 8  │               │
│    │  Workflow    │ │  xiansai DB │               │
│    │  Engine      │ │             │               │
│    └─────────────┘ └─────────────┘               │
│                                                     │
│  ┌──────────────────────────────────────────────┐ │
│  │              Xians.Agent.Lib                 │ │
│  │  (C# .NET Agent Runtime — NuGet: XiansAi.Lib)│ │
│  │  Agent 作为命令行应用运行                     │ │
│  │  Semantic Kernel + DataProcessor 双模式       │ │
│  └──────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
```

### 技术栈

| 组件 | 技术 |
|------|------|
| **Server** | .NET 9 / ASP.NET Core |
| **Database** | MongoDB 8 |
| **Workflow Engine** | Temporal.io |
| **Agent Runtime** | C# / .NET (XiansAi.Lib) |
| **UI (Portal)** | React 19 + Material-UI + Auth0/Entra ID |
| **UI (Agent Studio)** | Next.js 14 + Tailwind CSS v4 + shadcn/ui |
| **Web SDK** | TypeScript — SocketSDK / RestSDK / SSESDK |
| **Python SDK** | xians-lib-python |
| **LLM** | LLM-agnostic (OpenAI 默认) + Semantic Kernel |
| **Auth** | Keycloak / Auth0 / Entra ID (多 Provider) |

### 通信模式

平台支持 6 种通信模式，完整覆盖 User↔Agent↔Agent 场景：

| 方向 | 协议 | 场景 |
|------|------|------|
| User → Agent | REST (`/api/user/rest/converse`) | 同步对话 |
| User → Agent | WebSocket (`/ws/chat`) | 实时双向 |
| User → Agent | SSE (`/api/user/sse/events`) | 服务端推送 |
| Agent → User | Proactive Message | 主动触达 |
| Agent → Agent | Handoff / Forward | 转接 / 转发 |
| Agent ↔ Agent | Sync Chat / Data RPC / Events | 协作 |

### 消息类型：Chat vs Data

所有通信共用同一结构，但处理路径不同：

```json
{
  "messageType": 0,  // Chat → Semantic Kernel (NL理解)
  "messageType": 1   // Data  → DataProcessor (RPC方法调用)
}
```

**Chat** 消息走 Semantic Kernel 做自然语言理解；**Data** 消息绕过 LLM，直接路由到 `DataProcessor` 类做 RPC 调用。`text` 字段在 Data 模式中是方法名。

### Temporal.io 工作流引擎

平台核心依赖 Temporal.io 实现：
- **容错执行**：Activity 失败自动重试，断点可恢复
- **长时流程**：支撑数月/数年运行的长流程
- **确定性调度**：Flow Scheduling 支持定时/周期触发
- 配合 `Fault Tolerance` 和 `Sub-Flows` 机制

### 知识管理 & Memory

内置 3 种 Agent Memory 机制：
- **CAG** (Cache Augmented Generation) — 知识提示缓存
- **RAG** — 检索增强生成
- **Cache-Memory** — 短期记忆

Knowledge 通过 Portal 或 API 动态更新，无需改代码。

### 多租户

从设计层面支持多租户，每个租户独立配置 LLM Provider、Auth、加密密钥。

## 仓库一览

| 仓库 | 描述 |
|------|------|
| [XiansAi.Server](https://github.com/XiansAiPlatform/XiansAi.Server) | .NET 9 后端 — WebApi + LibApi + UserApi，Temporal + MongoDB |
| [XiansAi.Lib](https://github.com/XiansAiPlatform/XiansAi.Lib) | C# Agent 运行时 NuGet 包 — Semantic Kernel + DataProcessor |
| [XiansAi.UI](https://github.com/XiansAiPlatform/XiansAi.UI) | React 管理 Portal — Auth0/Entra ID/GitHub SSO |
| [agent-studio](https://github.com/XiansAiPlatform/agent-studio) | Next.js 14 Agent 可视化编排界面，Nordic 设计系统 |
| [sdk-web-typescript](https://github.com/XiansAiPlatform/sdk-web-typescript) | TypeScript SDK — SocketSDK / RestSDK / SSESDK (`@99xio/xians-sdk-typescript`) |
| [xians-lib-python](https://github.com/XiansAiPlatform/xians-lib-python) | Python SDK |
| [XiansAi.PublicDocs](https://github.com/XiansAiPlatform/XiansAi.PublicDocs) | MkDocs 文档站点 (docs.xians.ai) |
| [community-edition](https://github.com/XiansAiPlatform/community-edition) | Docker Compose 一键部署 — Server + UI + MongoDB + Temporal (Keycloak) |

## 快速启动 (Community Edition)

```bash
git clone https://github.com/XiansAiPlatform/community-edition.git
cd community-edition
cp .env.example .env
# 编辑 .env: OPENAI_API_KEY, KEYCLOAK_ADMIN_PASSWORD
./start-all.sh
```

| 服务 | URL | Credentials |
|------|-----|-------------|
| XiansAi Platform | localhost:3001 | admin / KEYCLOAK_ADMIN_PASSWORD |
| Temporal Web UI | localhost:8080 | admin / KEYCLOAK_ADMIN_PASSWORD |
| Keycloak Admin | localhost:18080/admin | admin / KEYCLOAK_ADMIN_PASSWORD |
| API Docs | localhost:5001/api-docs | 无需认证 |

## 关键设计理念

### Agentic Software vs 传统软件

| 维度 | 传统软件 | Xians.ai Agent |
|------|----------|----------------|
| 流程驱动 | 用户推动 (操作者) | Agent 自主驱动 (目标导向) |
| 系统演进 | 静态规则引擎 | 动态学习 + Agent-环境反馈循环 |
| 用户交互 | 表单 + 手动输入 | 多渠道自然语言 + Agent 主动触达 |

### 双执行模式

- **非确定性 (Conversational)**：Semantic Kernel + 自然语言，LLM 理解意图 → 响应
- **确定性 (Business Process)**：Temporal Workflow + Activity，RPC 风格方法调用

### MCP 集成

平台支持 MCP (Model Context Protocol) 扩展，Agent 可通过 MCP 调用外部工具和能力。

## 标签

#AI-Agent #Agent-Platform #.NET #Temporal #SemanticKernel #Multi-Agent #LLM-agnostic #Multi-Tenant #Enterprise #Workflow-Engine
