---
title: agentmemory
type: entity
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://github.com/jayzeng/agentmemory
  - https://jayzeng.github.io/agentmemory/
tags:
  - memory
  - markdown
  - local-first
  - coding-agent
  - Claude-Code
  - Codex
  - Cursor
---

# agentmemory

**agentmemory** 是 jayzeng 用 16 天、$1,000 单枪匹马开发的**本地优先 AI 编程记忆工具**，通过 npm 分发（`myagentmemory`）。核心理念：**持久记忆不需要服务器**——纯 Markdown 文件存储，qmd 驱动语义搜索，自动注入到 Claude Code / Codex / Cursor / Agent 的 SKILL.md 工作流中。

> ⚠️ 名称注意：不要与 Supermemory（`supermemoryai/supermemory`，SOTA 基准三冠王）混淆。agentmemory 是轻量本地工具，Supermemory 是完整 SaaS 平台。

## 核心能力

| 能力 | 说明 |
|------|------|
| **长期记忆** | 持久化 Markdown 文件，支持 qmd 语义/关键词/混合搜索 |
| **每日日志** | `~/.agent-memory/daily/` 按日期自动归档 |
| **主题/事件笔记** | `~/.agent-memory/topics/` 自由分类 |
| **Scratchpad** | 即时剪贴板，`/scratch` 命令读写 |
| **自动上下文注入** | 每次对话自动选择性注入相关历史记忆 |
| **跨平台安装** | Homebrew（macOS）、npm（全局）、Windows 支持 |

## 架构

```
~/.agent-memory/
├── daily/          ← 每日日志（YYYY-MM-DD.md）
├── topics/        ← 主题/事件笔记
├── facts/         ← 长期事实
└── scratch.md     ← 即时剪贴板

~/.claude/skills/agent-memory/SKILL.md   ← Claude Code 注入
~/.codex/skills/agent-memory/SKILL.md    ← Codex 注入
~/.cursor/skills/agent-memory/SKILL.md   ← Cursor 注入
~/.agents/skills/agent-memory/SKILL.md   ← Agent CLI 注入
```

**工具层**（`src/core.ts`）：
- `memory_write` — 写入指定类型文件
- `memory_read` — 读取记忆内容
- `memory_search` — qmd 语义/关键词/混合搜索
- `scratchpad_write` / `scratchpad_read` — 即时剪贴板

**搜索后端**：qmd（tobi/qmd）驱动，首次运行 `qmd embed` 生成向量索引，之后支持语义检索。

## 技术栈

- **语言**：TypeScript / Node.js（单一仓库）
- **分发**：npm（`myagentmemory`）、Homebrew tap
- **搜索**：qmd（本地向量嵌入）
- **存储**：纯 Markdown 文件（~/.agent-memory/）
- **集成**：SKILL.md 注入各大 Agent CLI

## 与 agentmemory（Jay Zeng）vs Supermemory 的区别

| 维度 | agentmemory（Jay Zeng）| Supermemory |
|------|----------------------|-------------|
| **Stars** | ~1,400（npm）| ~22,000 |
| **存储** | 本地 Markdown | 云端 |
| **架构** | CLI + SKILL.md | SaaS 平台 + SDK |
| **搜索** | qmd 本地向量 | 自有向量引擎 |
| **Agent 集成** | Claude Code / Codex / Cursor / Agent | MCP / SDK / API |
| **多 Agent 共享** | ❌ 各 Agent 独立记忆 | ✅ 共享知识库 |
| **部署** | 本地，零配置 | 需要注册 / Docker 自托管 |
| **基准测试** | 未公开 | LoCoMo #1, LongMemEval #1, ConvoMem #1 |

## 适用场景

### ✅ 选 agentmemory 当：
- 只用 Claude Code / Codex / Cursor其中一个
- 不想部署服务器，只想要本地笔记增强
- 团队共记忆不是刚需（各用各的）
- 快速验证记忆需求，不确定要不要上完整系统

### ❌ 不选 agentmemory 当：
- 需要多 Agent / 多用户共享记忆
- 需要公开基准成绩背书
- 需要企业级 RBAC / 多租户
- 想要 L0/L1/L2 分层抽象（ByteRover/OpenViking 方向）

## 安装

```bash
# npm 全局
npm install -g myagentmemory

# Homebrew（macOS）
brew tap jayzeng/agentmemory https://github.com/jayzeng/agentmemory
brew install jayzeng/agentmemory/agent-memory

# 初始化记忆目录
agent-memory init

# 注入 SKILL.md 到各平台
agent-memory install-skills

# 可选：启用 qmd 语义搜索
qmd collection add ~/.agent-memory --name agent-memory
qmd embed
```

## 相关项目

- [[Memory Systems]] — 记忆系统总体对比
- [[ByteRover]] — 编程 Agent 记忆 CLI，LoCoMo 96.1%，Git 式版本控制
- [[OpenViking]] — 字节跳动上下文数据库，L0/L1/L2 三层抽象
- [[Claude-Mem]] — Claude Code 专用插件，会话压缩
- [[Supermemory]] — 基准 SOTA，但为云端平台
