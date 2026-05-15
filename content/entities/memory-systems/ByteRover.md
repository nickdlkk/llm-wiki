---
title: ByteRover
type: entity
created: 2026-05-10
updated: 2026-05-10
sources:
  - https://github.com/campfirein/byterover-cli
  - https://docs.byterover.dev
  - https://arxiv.org/abs/2604.01599
tags:
  - memory
  - CLI
  - coding-agent
  - benchmarks
---

# ByteRover

**ByteRover**（CLI 工具 `brv`）是一个为 AI 编程 Agent 提供持久、结构化记忆的交互式 REPL CLI。支持 22+ 款 AI 编程工具（Cursor、Claude Code、Windsurf、Cline 等）。

## 核心能力

| 能力 | 说明 |
|------|------|
| 🌐 **Web Dashboard** | `brv webui` — 可视化管理记忆 |
| 🖥️ **TUI REPL** | React/Ink 驱动的交互式终端界面 |
| 🧠 **上下文树** | 将项目知识组织为可查询的上下文树 |
| 🔀 **Git 式版本控制** | 记忆分支、提交、合并、推送/拉取 |
| 🤖 **20 个 LLM 提供商** | Anthropic、OpenAI、Google、Groq、Mistral、xAI、DeepSeek 等 |
| 🛠️ **24 个内置工具** | 代码执行、文件操作、知识搜索、记忆管理等 |
| 🔌 **MCP 集成** | Model Context Protocol 支持 |
| 🏢 **企业代理支持** | 适用于企业内网环境 |

## 基准成绩

| 基准 | 成绩 |
|------|------|
| **LoCoMo** | 96.1%（1,982 题，272 文档）|
| **LongMemEval-S** | 92.8%（500 题，23,867 文档）|

> LoCoMo 测试超长对话（~20K tokens，35 会话），涵盖单跳、多跳、时序和开放域检索。
> LongMemEval-S 测试知识更新、时序推理和多会话综合能力。

## 技术架构

```
项目代码 / 文档
    ↓
上下文树（Context Tree）
    ↓
版本控制（branch/commit/merge）
    ↓
语义检索 + 工具调用
    ↓
Agent 上下文注入
```

## 使用场景

### 典型工作流

```bash
# 在项目目录中启动 REPL
brv

# 添加当前文件到上下文树
brv add src/main.py

# 提交当前状态
brv commit -m "添加用户认证模块"

# 推送到云端
brv push

# 切换分支
brv checkout feature/new-memory
```

## 与其他系统的区别

| 特性 | ByteRover | Supermemory | Honcho |
|------|-----------|-------------|--------|
| 定位 | 编程 Agent 记忆 | 通用 Agent 记忆 | 用户/会话记忆 |
| 界面 | CLI + Web UI | 云 + SDK | 托管/自托管 |
| 版本控制 | Git 式分支/合并 | 无 | 无 |
| 基准成绩 | LoCoMo 96.1% | #1（LongMemEval等）| LoCoMo 86.9% |
| 专注方向 | 代码上下文 | 知识管理 | 用户建模 |

## Hub 与连接器生态

ByteRover 提供 Skill Bundle 连接器市场，可以扩展：
- 项目管理工具集成
- 代码托管平台同步
- CI/CD 流水线记忆

## 相关项目

- [[Memory Systems]] — 记忆系统总体对比
- [[Supermemory]] — 基准排名第一
- [[OpenViking]] — Volcengine 出品的上下文数据库
