---
title: lsbot
description: Lean & Secure Bot — 端到端加密、本地优先、单二进制文件的 AI Bot，支持 OpenClaw MCP 协议
github: ruilisi/lsbot
stars: 409
forks: 57
tags: [lsbot, bot, mcp-server, openclaw, secure, e2e-encryption, local-first, go, single-binary]
created: 2026-05-16
updated: 2026-05-16
sources: [https://github.com/ruilisi/lsbot, https://lsbot.org]
---

# lsbot — Lean & Secure Bot

## 简介

lsbot 取意 **Lean Secure Bot**，名字呼应 `ls` — Unix 最基础的命令、第一次拿到新机器时你敲的第一个命令。像 `ls` 一样，lsbot 是一个基础工具：精简、专注、永远可用。

核心理念：**你的数据属于你**。所有 relay 流量使用 P-256 ECDH + AES-256-GCM 端到端加密，中继服务器只看到密文；单静态二进制，无运行时、无包管理器、无依赖链；本地优先，对话历史、记忆、配置、凭证全部存储在本地机器。

## 核心问题：Bot 需要安全性

当你运行一个 AI Bot，你实际上把机器的钥匙交了出去：文件、终端、数据库、浏览器会话、凭证。OpenClaw（以及大多数同类工具）以便利性和功能广度为设计目标，安全性是事后考虑：

| 问题 | 风险 |
|------|------|
| 消息通过开发者云服务路由 | 服务商可读取每条消息 |
| 无端到端加密 | 任何中继节点可审查内容 |
| npm 依赖链 | 500+ 第三方包，攻击面大 |
| Node.js 运行时 | 动态执行环境，攻击面庞大 |
| 无本地数据保证 | 对话历史、上下文、记忆存储位置不明 |

lsbot 的设计前提：**中继服务器是不可信的**。这不是对 lsbot.org 的不信任，而是一个工程原则——不需要信任的地方，就不应该建立信任依赖。

## 核心功能

- **端到端加密，默认开启**：P-256 ECDH + AES-256-GCM，中继服务器只看到密文
- **单一静态二进制**：无可运行时、无包管理器、无依赖链，攻击面仅一个可审计二进制
- **本地优先**：对话历史存浏览器 IndexedDB（本地），E2EE 私钥存 `~/.lsbot.pem`，配置/API 密钥存 `~/.lsbot.yaml`，Cron 任务存 `~/.lingti.db`，Skills 存 `~/.lingti/skills/`
- **密钥验证带外进行**：从 `lsbot e2e pubkey` 手动获取公钥，核对指纹，防止中继服务器替换密钥

## 快速开始

**两步启动，无需平台账号，无需公网服务器：**

```bash
# macOS / Linux / WSL
curl -fsSL https://files.lingti.com/install-lsbot.sh | bash

# Windows (PowerShell)
irm https://files.lingti.com/install-lsbot.ps1 | iex
```

```bash
lsbot relay --provider deepseek --api-key sk-xxx
```

启动后自动输出 Bot 页面地址，国内用户可通过 `https://lsbot.net/bots/xxx` 访问。

支持的 `--provider`：`deepseek`、`claude`、`kimi`、`minimax`、`gemini`、`openai` 等。

### 端到端加密流程

1. Bot 首次启动时，在 `~/.lsbot.pem` 自动生成持久化 P-256 密钥，日志打印指纹
2. 浏览器打开 Bot Page，点击状态栏 **Secure** 进入设置面板
3. 从终端运行 `lsbot e2e pubkey`，将公钥粘贴到面板，核对指纹一致后点击 **Activate**
4. 状态栏出现**蓝色锁图标**，此后页面刷新自动恢复加密

```bash
# 查看本机公钥和指纹
lsbot e2e pubkey

# 手动生成密钥对
lsbot e2e keygen --save
```

## 数据存储

| 数据 | 存储位置 | 云端是否存在 |
|------|----------|:---:|
| 对话历史 | 浏览器 IndexedDB（本地） | ❌ |
| E2EE 私钥 | `~/.lsbot.pem` | ❌ |
| 配置文件 | `~/.lsbot.yaml` | ❌ |
| AI API 密钥 | `~/.lsbot.yaml` | ❌ |
| Cron 任务数据库 | `~/.lingti.db` | ❌ |
| 技能文件 | `~/.lingti/skills/` | ❌ |

云中继服务器（`lsbot.org`，国内镜像 `lsbot.net`）只做一件事：**路由加密后的消息**。消息转发后立即丢弃。

## 技术规格

- **语言**: Go
- **许可证**: 未指定
- **官网**: https://lsbot.org
- **国内镜像**: https://lsbot.net
- **文档**: https://lsbot.org/docs

## 许可证

未指定（仓库无 license 文件）

## 相关链接

- GitHub: https://github.com/ruilisi/lsbot
- 官网: https://lsbot.org
- 国内镜像: https://lsbot.net
