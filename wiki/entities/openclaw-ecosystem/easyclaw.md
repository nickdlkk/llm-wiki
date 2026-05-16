---
title: EasyClaw
description: One-click installer for OpenClaw AI agent. Download → Run → Enter API key, three steps. macOS & Windows support.
github: ybgwon96/easyclaw
stars: 118
forks: 35
tags: [openclaw, installer, desktop-app, electron, react, tailwindcss, typescript, macos, windows, telegram-bot, one-click, ai-agent]
created: 2026-05-16
updated: 2026-05-16
sources: [https://github.com/ybgwon96/easyclaw, https://easyclaw.kr]
---

# EasyClaw

## 简介

EasyClaw 是一个桌面安装器，让用户**无需任何终端命令**即可完成 [OpenClaw](https://github.com/openclaw/openclaw) AI agent 的安装配置。核心理念：Download → Run → Enter API key，三步完成。

## 核心功能

- **一键安装**：自动检测并安装 WSL、Node.js 和 OpenClaw
- **多 AI Provider 支持**：Anthropic Claude、Google Gemini、OpenAI、 MiniMax、GLM
- **Telegram 集成**：通过 Telegram Bot 随时随地使用 AI agent
- **跨平台**：支持 macOS (Intel + Apple Silicon) 和 Windows

## 技术栈

| 领域 | 技术 |
|------|------|
| 框架 | Electron + electron-vite |
| 前端 | React 19 + Tailwind CSS 4 |
| 语言 | TypeScript |
| 构建/CI | electron-builder + GitHub Actions |
| 代码签名 | Apple Notarization (macOS) / SignPath (Windows, 申请中) |

## 项目结构

```
src/
├── main/             # 主进程 (Node.js)
│   ├── services/     # 环境检测、安装器、入门引导、网关
│   └── ipc-handlers  # IPC 通道路由
├── preload/          # contextBridge (IPC API 桥接)
└── renderer/         # React UI (7 步向导)
api/                  # Vercel Serverless Functions
docs/                 # 官网 (easyclaw.kr)
```

## 下载地址

| 操作系统 | 文件格式 | 下载链接 |
|----------|----------|----------|
| macOS | `.dmg` | [Download](https://github.com/ybgwon96/easyclaw/releases/latest/download/easy-claw.dmg) |
| Windows | `.exe` | [Download](https://github.com/ybgwon96/easyclaw/releases/latest/download/easy-claw-setup.exe) |

也可访问 [easyclaw.kr](https://easyclaw.kr) 下载，自动检测操作系统。

## 许可证

MIT License

## 相关链接

- GitHub: https://github.com/ybgwon96/easyclaw
- 官网: https://easyclaw.kr
