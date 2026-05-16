---
title: Hermes Agent
description: Nous Research 出品的自进化 AI Agent，内置学习循环、自动创建 Skills、持久记忆、 Cron 调度、多 Provider 支持
github: nickdlkk/hermes-agent
stars: 0
tags: [hermes-agent, nous-research, ai-agent, self-improving, skill-creation, memory, scheduled-automation, mcp, python]
created: 2026-05-16
updated: 2026-05-16
sources: [https://github.com/nickdlkk/hermes-agent, https://hermes-agent.nousresearch.com]
---

# Hermes Agent

## 简介

Hermes Agent 是 [Nous Research](https://nousresearch.com) 出品的**自进化 AI Agent**，核心理念："The agent that grows with you"。它能通过经验创建 Skills，在使用中自我改进，将自身知识持久化存储，跨会话深度建模用户画像。

支持任意模型接入：Nous Portal、OpenRouter (200+ 模型)、NVIDIA NIM (NeMo)、Xiaomi MiMo、z.ai/GLM、Kimi/Moonshot、MiniMax、Hugging Face、OpenAI，或自有端点。通过 `hermes model` 切换模型无需修改代码。

## 核心功能

- **真实终端界面**：完整 TUI，多行编辑，斜杠命令自动补全，会话历史、插播重定向、流式工具输出
- **多平台消息网关**：Telegram、Discord、Slack、WhatsApp、Sigmail、CLI — 单网关进程统一管理
- **闭合学习循环**：Agent 驱动的记忆 + 周期性 Nudge；复杂任务后自动创建 Skill；使用中 Skills 自我改进；FTS5 会话搜索 + LLM 摘要实现跨会话回调；[Honcho](https://github.com/planets-org/honcho) 对话用户建模
- **定时自动化**：内置 Cron 调度器，多平台投递；每日报告、每日备份、周末提醒 — 全程自然语言运行
- **并行化委托**：为并行工作流展开独立子 Agent；用 Python 脚本通过 RPC 调用工具，将多步 Pipeline 压缩为零上下文成本轮次
- **任意环境运行**：六大终端后端 — 本地/Docker/SSH/Daytona/Singularitiva/Modal；Daytona 和 Modal 提供服务端持久性，Agent 环境隔由于闲时休眠和唤醒几乎零成本；支持 $5 VPS 或 GPU 集群

## 快速开始

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装后：

```bash
source ~/.bashrc      # 重载 shell (或 source ~/.zshrc)
hermes                # 启动聊天 CLI
hermes model          # 选择 LLM provider 和模型
hermes tools          # 配置启用的工具
hermes config set     # 设置单个配置值
hermes gateway        # 启动消息网关 (Telegram, Discord 等)
hermes setup          # 完整配置向导
hermes claw migrate   # 从 OpenClaw 迁移 (如从 OpenClaw 切换)
hermes update         # 更新到最新版本
hermes doctor         # 诊断问题
```

## 技术规格

- **语言**: Python
- **许可证**: MIT
- **官网**: https://hermes-agent.nousresearch.com
- **文档**: https://hermes-agent.nousresearch.com/docs/

## 消息网关命令参考

| 操作 | CLI | 消息平台 |
|------|-----|----------|
| 开始聊天 | `hermes` | — |
| 新会话 | `/new` 或 `/reset` | `/new` 或 `/reset` |
| 切换模型 | `/model [provider:model]` | `/model [provider:model]` |
| 设置人格 | `/personality [name]` | `/personality [name]` |
| 重试/撤销 | `/retry`, `/undo` | `/retry`, `/undo` |
| 压缩上下文/查看使用 | `/compress`, `/usage`, `/insights [--days N]` | `/compress`, `/usage`, `/insights [days]` |
| 浏览 Skills | `/skills` 或 `/<skill-name>` | `/skills` 或 `/<skill-name>` |
| 插播当前工作 | Ctrl+C 或发送新消息 | `/stop` 或发送新消息 |
| 平台状态 | `/platforms` | `/status`, `/setphone` |

## 从 OpenClaw 迁移

首次运行 `hermes setup` 时，自动检测 `~/.openclaw` 并提供迁移选项。

```bash
hermes claw migrate              # 交互式迁移 (完整预设)
hermes claw migrate --dry-run    # 预览迁移内容
hermes claw migrate --preset user-data    # 迁移用户数据（不含 secrets）
hermes claw migrate --overwrite  # 覆写现有配置冲突
```

迁移内容：SOUL.md (人格)、Memories (MEMORY.md + USER.md entries)、Skills (user-created skills)、Command allowlist、Platform configs & allowed users、API keys (Telegram, OpenRouter, Anthropic, ElevenLabs)、TTS assets、Workspace instructions (AGENDA.md with `--workspace-target`)

## 许可证

MIT License

## 相关链接

- GitHub: https://github.com/nickdlkk/hermes-agent
- 官网: https://hermes-agent.nousresearch.com
- Discord: https://discord.gg/NousResearch
