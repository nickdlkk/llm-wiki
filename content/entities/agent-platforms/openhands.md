---
title: OpenHands
tags:
  - AI驱动开发
  - SDK
  - Python
  - SWEBench-77.6
  - Agent
  - MCP
  - Docker
  - 自托管
---

# OpenHands

> **Repo**: [All-Hands-AI/OpenHands](https://github.com/All-Hands-AI/OpenHands)
> **SDK**: [OpenHands/software-agent-sdk](https://github.com/OpenHands/software-agent-sdk)
> **License**: MIT（核心）；enterprise/ 目录为商业许可
> **Benchmark**: SWEBench 77.6%
> **定位**: AI驱动开发平台 — 软件Agent SDK + CLI + Local GUI + Cloud + Enterprise

## 核心定位

OpenHands 是 All-Hands-AI 社区的产品，定位是 **AI-Driven Development**，被 TikTok/Amazon/Google/Netflix/NVIDIA 等企业的工程师使用。

有四种使用方式：

| 产品 | 说明 |
|---|---|
| **Software Agent SDK** | Python 库，定义 Agent 并本地运行或 scale 到云端 1000+ Agent |
| **OpenHands CLI** | 类 Claude Code 体验的终端 CLI，支持 TUI/Headless/Web |
| **OpenHands Local GUI** | 本地浏览器 IDE（类 Devin/Jules 体验），REST API + React SPA |
| **OpenHands Cloud** | 托管版（app.all-hands.dev），免费用 Minimax 模型，含 Slack/Jira/Linear 集成、RBAC |
| **OpenHands Enterprise** | 企业自托管（Kubernetes），source-available，commercial license |

## 技术栈

| 层 | 选型 |
|---|---|
| 语言 | Python 3.12+ |
| LLM 接口 | litellm（模型无关，支持 Claude/GPT/任意 provider） |
| Agent Server | FastAPI, WebSocket, aiosqlite, Docker SDK |
| 工具层 | browser-use, libtmux, bashlex, python-frontmatter |
| MCP | FastMCP (原生 MCP Server 支持) |
| 前端 | React SPA（Local GUI） |
| 部署 | Docker（核心镜像 MIT 许可）；Kubernetes（Enterprise） |
| 评测 | SWEBench 77.6% |

## SDK Monorepo 结构

```
software-agent-sdk/ (UV workspace)
├── openhands-sdk/          # 核心SDK：LLM/Agent/Conversation/Tool
├── openhands-tools/        # 内置工具：Terminal/FileEditor/TaskTracker/Browser/...
├── openhands-workspace/    # 工作区管理
├── openhands-agent-server/ # REST/WebSocket 后端：Docker容器编排
├── examples/              # 3个示例：standalone/remote/CI
└── tests/
```

### 核心 SDK 类

```python
from openhands.sdk import LLM, Agent, Conversation, Tool
from openhands.tools.file_editor import FileEditorTool
from openhands.tools.task_tracker import TaskTrackerTool
from openhands.tools.terminal import TerminalTool

llm = LLM(
    model="anthropic/claude-sonnet-4-5-20250929",
    api_key=os.getenv("LLM_API_KEY"),
)

agent = Agent(
    llm=llm,
    tools=[
        Tool(name=TerminalTool.name),
        Tool(name=FileEditorTool.name),
        Tool(name=TaskTrackerTool.name),
    ],
)

conversation = Conversation(agent=agent, workspace=cwd)
conversation.send_message("Write 3 facts about the current project into FACTS.txt.")
conversation.run()
```

| 类 | 职责 |
|---|---|
| **LLM** | 模型无关接口（litellm 支持 100+ LLM） |
| **Agent** | 主体，配置 LLM + tools + skills |
| **Conversation** | 管理消息历史、发送消息、触发执行循环 |
| **Tool** | 工具定义（name + 运行时绑定） |

## Agent Server 架构

```
客户端 (CLI/GUI/SDK)
    │
    │ REST / WebSocket
    ▼
AgentServer (FastAPI + WebSocket)
    │
    ├── 工作区隔离（Docker 容器）
    │     └── VSCode extensions 内置
    │
    ├── SQLite (aiosqlite + Alembic migrations)
    │
    └── Docker SDK（容器生命周期管理）
```

AgentServer 支持：
- **REST API**：任务管理、状态查询
- **WebSocket**：实时流式输出
- **Docker 隔离**：每个 Agent 运行在独立容器内
- **内置 VSCode 扩展**：代码编辑、调试

## 工具层

| 工具 | 说明 |
|---|---|
| **TerminalTool** | 在容器/workspace 内执行命令 |
| **FileEditorTool** | 文件读写、编辑 |
| **TaskTrackerTool** | 任务追踪 |
| **BrowserTool** | browser-use 驱动的浏览器自动化 |
| **TmuxTool** | libtmux 多终端会话管理 |

也支持自定义 Tool 和 **MCP Server**：通过 `FastMCP` 原生接入 MCP 协议扩展。

## Skills 系统

Agent 可通过 `AgentContext(load_public_skills=True)` 加载公开 skills 市场（`OpenHands/extensions`），支持：
- `uv` 技能：自动识别 `uv.lock`，提供包管理指导
- `deno` 技能：自动识别 `deno.json/deno.lock`
- 社区贡献的任意 skills

## CLI 运行模式

| 模式 | 命令 | 场景 |
|---|---|---|
| **Terminal (TUI)** | `openhands` | 交互式开发 |
| **IDE Integration (ACP)** | `openhands acp` | Toad/Zed/VSCode/JetBrains |
| **Headless** | `openhands --headless -t "task"` | CI、脚本、自动化 |
| **Web Interface** | `openhands web` | 浏览器 TUI |
| **GUI Server** | `openhands serve` | 完整 Web GUI |

## Confirmation Modes

Agent 执行危险操作前可配置确认模式，人类介入控制 Agent 行为。

## MCP 集成

```bash
# 添加 MCP Server
openhands mcp add tavily --transport stdio \
  npx -- -y mcp-remote "https://mcp.tavily.com/mcp/?tavilyApiKey=<key>"

# 启用/禁用
openhands mcp enable <server>
openhands mcp disable <server>
```

## 评测基准

**SWEBench 77.6%** — 表明 OpenHands 在真实软件工程任务上的表现（77.6% 的 SWE-bench 任务可成功解决）。

## 与 Kandev / Coder Studio / Paperclip 对比

| | OpenHands | Kandev | Coder Studio | Paperclip |
|---|---|---|---|---|
| **核心** | SDK + 端到端平台 | 多Agent并行编排 | 单会话监督 IDE | 企业多Agent编排 |
| **LLM** | 任意（litellm） | 任意 | 任意 | 任意 |
| **评测分** | SWEBench 77.6% | — | — | — |
| **工作流** | 无内置模板 | 5个内置模板 | Supervisor (Phase 3) | 多租户工作流 |
| **协议** | ACP (litellm) | ACP (JSON-RPC 2.0) | Provider hooks | 适配器层 |
| **License** | MIT（核心） | AGPL-3.0 | MIT | 私有 |
| **Enterprise** | 有（商业许可） | 无 | 无 | 有 |

## 安装

```bash
# CLI（推荐，uv 安装）
uv tool install openhands --python 3.12

# 或 standalone binary
curl -fsSL https://install.openhands.dev/install.sh | sh

# SDK
pip install openhands-sdk openhands-tools
```

## 关键论文

```
@misc{wang2025openhandssoftwareagentsdk,
  title={The OpenHands Software Agent SDK},
  author={Xingyao Wang, Simon Rosenberg, Juan Michelini, Calvin Smith,
          Hoang Tran, Engel Nyst, Rohit Malhotra, Xuhui Zhou,
          Valerie Chen, Robert Brennan, Graham Neubig},
  year={2025}, eprint={2511.03690}, archivePrefix={arXiv}
}
```
