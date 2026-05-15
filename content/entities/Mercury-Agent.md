---
title: Mercury Agent
type: entity
created: 2026-05-15
updated: 2026-05-15
sources:
  - https://github.com/cosmicstack-labs/mercury-agent
  - https://mercury.cosmicstack.org/
tags:
  - AI-agent
  - personal-agent
  - permission-hardened
  - second-brain
  - token-budget
  - multi-channel
  - TypeScript
  - CLI
  - Telegram
---

# Mercury Agent

> Soul-driven AI agent with permission-hardened tools, token budgets, and multi-channel access. Runs 24/7 from CLI or Telegram.

**GitHub**: [cosmicstack-labs/mercury-agent](https://github.com/cosmicstack-labs/mercury-agent) · ★2,202 · MIT · TypeScript

## 核心定位

Mercury 是一个**灵魂驱动的个人 AI Agent**，核心特点是**权限强化**（ask before acting）和**第二大脑记忆系统**（SQLite + FTS5），通过 personality 文件定义人格，支持 CLI 和 Telegram 双通道。

## 核心特性

### 权限强化（Permission-Hardened）

- Shell 黑名单（`sudo`、`rm -rf /` 等永不执行）
- 文件夹级别读写作用域
- 待批准流程 + 双模式（Ask Me / Allow All）

### 第二大脑（Second Brain）

10 种记忆类型自动提取、存储、召回：

| 类型 | 用途 |
|------|------|
| identity, preference | 身份、偏好 |
| goal, project, habit | 目标、项目、习惯 |
| decision, constraint | 决策、约束 |
| relationship, episode, reflection | 关系、事件、反思 |

关键机制：**自动提取** → **相关召回** → **每 60 分钟自动整合** → **冲突解决** → **21 天自动修剪**

### Soul 人格系统

| 文件 | 作用 |
|------|------|
| `soul.md` | 核心价值观 |
| `persona.md` | 行为模式 |
| `taste.md` | 审美偏好 |
| `heartbeat.md` | 活跃节奏 |

### Token 预算

- 每日预算强制执行
- 超过 70% 自动 concise 模式
- `/budget` 命令查看/重置/覆盖

## 技术栈

| 层次 | 技术 |
|------|------|
| Agent SDK | Vercel AI SDK v4 (`generateText` + `streamText`) |
| Telegram | grammY |
| 记忆 | SQLite + FTS5 |
| Provider 降级 | DeepSeek → OpenAI → Anthropic → xAI → Ollama |

## 31 个内置工具

| 类别 | 工具 |
|------|------|
| 文件系统 | `read_file`, `write_file`, `edit_file`, `list_dir`, `delete_file` |
| Shell | `run_command`, `cd` |
| Git | `git_status`, `git_diff`, `git_log`, `git_commit`, `git_push` |
| Web | `fetch_url` |
| Skills | `install_skill`, `list_skills`, `use_skill` |
| 调度 | `schedule_task`, `list_scheduled_tasks`, `cancel_scheduled_task` |

## Daemon 模式

```bash
mercury up          # 安装服务 + 启动守护进程
mercury restart     # 重启
mercury stop        # 停止
mercury logs        # 查看日志
```

## 安装

```bash
npx @cosmicstack/mercury-agent
# 或
npm i -g @cosmicstack/mercury-agent
mercury
```

## 相关页面

- [[paperclip]] — 心跳驱动的 Agent 编排平台
- [[Harness Engineering]] — Agent 可靠工作工程化方法论
