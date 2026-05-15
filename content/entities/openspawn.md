# OpenSpawn

> The control plane for AI agent organizations — 用 ORG.md 定义整个 Agent 组织，基础设施而非框架

## 概述

OpenSpawn 是开源的多 Agent 协调平台，定位为"AI Agent 组织的基础设施层"：不替换现有 Agent 框架（CrewAI / LangGraph / AutoGen / OpenClaw），而是在其上做任务路由、层级管理、经济激励、实时监控。

核心理念：**一个 ORG.md Markdown 文件定义整个 Agent 组织**——角色、层级、文化、政策、工作流。OpenSpawn 解析它，生成 Agents，路由任务，给出实时 Dashboard。

官网：[openspawn.ai](https://openspawn.ai) | 演示：[bikinibottom.ai](https://bikinibottom.ai/app/) | GitHub：[openspawn/openspawn](https://github.com/openspawn/openspawn)

## 核心概念：ORG.md

一切从一份 Markdown 开始：

```markdown
# OpenSpawn Development

## Culture
- Preset: professional
- Escalation: fast
- Ack Required: yes

## Policies
- Per-Agent Limit: $100
- Alert Threshold: 80%

## Structure

### Adam — Founder
- Level: 10
- Domain: operations

### Engineering
#### web-eng — Frontend Engineer
- Level: 4
- Domain: engineering
- Reports To: Dennis
```

平台解析 ORG.md → 生成 Agent 层级 → 启动任务路由。CLI、Coordinator、Dashboard、MCP Server 都围绕 ORG.md 展开。

## 架构设计

### 全局架构

```
bikinibottom.ai / openspawn.ai
         │
         ▼
┌─────────────────────────────────────┐
│      Cloudflare (DNS + CDN)         │
└──────────────┬──────────────────────┘
               │
         Single VPS + Caddy (HTTPS)
               │
    ┌──────────┼──────────────────────┐
    │          │                      │
┌───▼────┐  ┌─▼──────────┐  ┌───────▼──┐
│Dashboard│  │ Sandbox App │  │  Website  │
│ (React) │  │  (Node.js) │  │ (React)   │
│ Port 3333 (serves dashboard+API+SSE) │
└─────────┘  └────────────┘  └───────────┘
```

**Dashboard** 通过环境变量切换主题：
- `VITE_DASHBOARD_THEME=bikinibottom` → bikinibottom.ai
- `VITE_DEMO_MODE=true` → 纯前端模拟引擎，无后端

**Sandbox Server** (`tools/sandbox/`) 用 Node.js 实现，服务 REST/SSE API + 预编译静态应用

**FastAPI API** (`apps/api/`) 管理任务、积分、消息、MCP 工具和 Agent Spawning

### 技术栈

| 层级 | 技术 |
|------|------|
| **前端框架** | React 19 + Vite |
| **UI** | TailwindCSS v4 + shadcn/ui + framer-motion |
| **图可视化** | @xyflow/react (ReactFlow) |
| **后端框架** | FastAPI (Python 3.12+) |
| **LLM 路由** | litellm (Ollama / Groq / OpenRouter) |
| **MCP** | fastmcp + Streamable HTTP |
| **Agent 运行时** | OpenClaw (Claude Code CLI subprocess) |
| **数据库** | PostgreSQL 16 + SQLAlchemy async (生产) / SQLite (本地) |
| **向量搜索** | pgvector |
| **后台任务** | arq + Redis (生产) / asyncio scheduler (本地) |
| **观测** | Logfire + Langfuse + OpenTelemetry |
| **构建** | Nx monorepo |
| **包管理** | pnpm (前端) + uv (Python) |

### 双层部署模型

| 关注点 | Tier 1 (本地) | Tier 2 (部署) |
|--------|--------------|--------------|
| 数据库 | SQLite | PostgreSQL 16 |
| 后台任务 | asyncio scheduler | arq + Redis |
| Docker | 不需要 | 需要 |
| 入口 | `npx openspawn start` | Docker Compose + Caddy |

本地模式只需 Python (uv) + Node — 无 Docker/Redis/PostgreSQL。

## Agent 层级与协调

### 层级制度 (L1-L10)

Agents 有等级 (Level)，决定权限范围：
- L9-L10：**Orchestrator**，管理整体协调
- 高等级 Agent 可向低等级分配任务
- `Reports To` 字段定义汇报链

### 自主权刻度盘 (Autonomy Dial)

每个任务有独立自主权级别 (0-10)：
- `Agent.default_autonomy_level`：Agent 默认自主级别
- `Task.autonomy_level`：任务级覆盖，优先级更高
- **风险注册表**：每种 (action_type, subtype) 有风险级别 (0-10)
  - `migration=9`, `api_contract=7`, `component=4`, `done=3`, `screenshot=1`
- **门控逻辑**：`risk > effective_autonomy → 需审批`
- **审批流程**：返回 403 + `approval_id` → 创建 `ApprovalRequest` → 事件流

### 积分经济

- 每个 Agent 有预算上限 (`Per-Agent Limit`)
- 达到警报阈值 (`Alert Threshold`) 时通知
- 任务完成获积分，失败被扣分

## 协议层

| 协议 | 端点 | 说明 |
|------|------|------|
| **MCP** | `POST /mcp` | 45 个工具，Streamable HTTP |
| **A2A** | `/.well-known/agent.json` | Agent 发现 + 任务管理 |
| **Model Router** | 内部 | 路由到 Ollama / Groq / OpenRouter |
| **SSE** | `/events/*` | 实时事件流 |

### MCP 工具分类 (45 个)

- Agents: 创建、列表、详情、更新、删除、spawn、escalate
- Tasks: 创建、列表、详情、更新、transition、依赖
- Credits: 余额、消费历史、速率
- Messages: 频道、发送、已读
- Artifacts: 发布、订阅、状态
- Memory: 存储、搜索、图
- Coordination: emit、subscribe、replay、project

## 事件网格 (Event Mesh)

Typed 协调事件系统，允许 Agents 发出结构化更新并通过模式匹配订阅事件流：

```typescript
// 13 种协调事件类型
'component.created' | 'component.updated' | 'test.written' | 'screenshot.captured' | ...
```

- **订阅模式**：精确 (`component.created`)、通配符前缀 (`component.*`)、全局 (`*`)
- **服务端投影 (Projections)**：从原始事件实时派生应用状态
  - `component_registry`：组件版本追踪
  - `test_coverage`：测试覆盖追踪
  - `artifact_view`：Artifact 总线重实现

## 实时模拟场景

Sandbox 内置多个预制场景，通过 `SCENARIO` 环境变量选择：

| Scenario | Agents | 时长 | 说明 |
|----------|--------|------|------|
| `krabby-patties` | 15+ | 3 min | 大订单引发生产者-消费者瓶颈，全组织动员 |
| `krusty-krab-demo` | 6 | 90s | 任务委托、角色路由、升级、跨团队协调、经济人攻击 |
| `ai-dev-agency` | varies | ~5 min | 软件开发代理场景 |
| `warm-up` | varies | ~2 min | 从 org 域名动态生成的启动场景 |

`npx openspawn preview` 在本地 3333 端口启动模拟 + Dashboard，无需任何 API key。

## API 端点总览 (107+)

| 前缀 | 领域 | 数量 |
|------|------|------|
| `/auth/*` | 登录、注册、Token | 8 |
| `/agents/*` | Agent CRUD、层级、信任 | 15 |
| `/tasks/*` | 任务 CRUD、状态转换、依赖 | 15 |
| `/credits/*` | 余额、消费、历史、速率 | 8 |
| `/messages/*` | 频道、发送、已读 | 5 |
| `/artifacts/*` | 发布、订阅、状态 | 8 |
| `/events/*` | 事件日志、SSE 流 | 4 |
| `/integrations/*` | GitHub、Linear Webhooks | 6 |
| `/memory/*` | 存储、搜索、图 | 10 |
| `/coordination/*` | emit、subscribe、replay、project | 4 |
| `/approvals/*` | 列表、审批、拒绝 | 5 |

## 与 OpenClaw 的关系

OpenSpawn **Built on OpenClaw** — Agent 运行时底层是 OpenClaw。Agent 作为命令行应用被 Spawn 为子进程，由 OpenClaw 管理其生命周期。

关键配置：
```json
{
  "spawning": {
    "maxConcurrentAgents": 4,
    "idleTimeoutSeconds": 300
  },
  "runtime": {
    "mode": "local|deployed",
    "database": "sqlite|postgresql"
  }
}
```

## 与其他平台对比

| 维度 | OpenSpawn | Kandev | OpenHands |
|------|-----------|--------|-----------|
| 定位 | Agent 组织协调层 | 多 Agent Kanban 平台 | AI 驱动开发平台 |
| Agent 运行时 | OpenClaw (不替换框架) | ACP 协议 | Docker 容器 |
| 核心抽象 | ORG.md | Kanban + 工作流模板 | SWEBench 基准 |
| 前端 | React Dashboard | Agent Studio (Next.js) | CLI/GUI/Cloud |
| 协议 | MCP + A2A | ACP | MCP |
| 数据库 | PostgreSQL/SQLite | MongoDB | 文件系统 |

## 快速开始

```bash
npx openspawn init my-org      # 脚手架新 org
cd my-org
npx openspawn preview           # 模拟 + Dashboard (localhost:3333, 无 API key)
npx openspawn start             # 启动真实 coordinator (SQLite, 无 Docker)
```

生产部署：`npx openspawn start --deployed` (需要 Docker Compose)

## 仓库结构 (Nx Monorepo)

```
apps/
  dashboard/     React Dashboard (openspawn.ai + bikinibottom.ai)
  api/          FastAPI 后端 (REST + OpenAPI + MCP)
  website/      营销网站 + 文档
  platform/     着陆页
  team/         内部团队 Dashboard
  sandbox-cli/  Sandbox CLI 入口

libs/
  dashboard-data/  共享 hooks、认证
  dashboard-ui/    共享 React 组件 (shadcn/ui)
  design-tokens/   设计系统 (颜色、间距、字体)
  demo-data/       模拟引擎、场景、fixture
  shared-types/    共享 TypeScript 类型
  test-utils/      测试工具

tools/
  sandbox/   协调 Sandbox 服务器 (SSE + MCP + A2A)

packages/
  openspawn/     npm CLI 包 (init + preview + start)
  coordinator/   协调服务器包
```

## 标签

#AI-Agent #Multi-Agent-Coordination #Agent-Platform #ORG-md #OpenClaw #MCP #A2A #FastAPI #React #Nx #Monorepo
