---
title: nyaterm
type: entity
created: 2026-05-29
updated: 2026-05-29
sources:
  - https://github.com/nyakang/nyaterm
tags:
  - terminal
  - ssh-client
  - sftp
  - ai-assistant
---

# nyaterm

> 📄 **原文**：[nyakang/nyaterm](https://github.com/nyakang/nyaterm) · 106 ⭐ · MIT License

**NyaTerm** 是基于 Tauri + React + Rust 构建的现代化 SSH 桌面客户端，定位是替代 Xshell、MobaXterm 等传统终端工具，提供 AI 辅助、多会话管理、SFTP、OTP 和加密同步能力。

## 核心定位

| 维度 | 描述 |
|------|------|
| **定位** | 面向开发者 / SysAdmin / DevOps 的 SSH 客户端 + 终端工作区 |
| **技术栈** | Tauri（Rust 后端）+ React 19 + TypeScript + TailwindCSS v4 |
| **平台** | Windows · macOS · Linux |
| **协议** | SSH、Telnet、Serial（串口）、本地 Shell |
| **许可** | MIT |

## 架构特点

### 前端（React + Tauri）

- **React 19** + TypeScript，使用 Vite 7 构建
- **xterm.js**（`@xterm/xterm` v6）作为终端模拟器核心，含 Fit/Search/WebGL addons
- **TailwindCSS v4**（`@tailwindcss/vite`）+ shadcn/ui 组件体系
- **Radix UI** + **CVA**（class-variance-authority）构建组件
- **i18next** + **react-i18next** 多语言支持（zh-CN/en）
- **Motion**（Framer Motion 分支）做 UI 动画
- **cmdk** 命令面板，`react-hotkeys-hook` 快捷键
- `next-themes` 主题管理（暗/亮模式）
- Markdown 渲染（`react-markdown` + `remark-gfm`）+ 代码高亮（`react-syntax-highlighter`）

### Rust 后端

- 通过 `tauri-apps/plugin-*` 处理系统层能力
- TOTP/HOTP OTP 管理（TOTP 种子 + SSH 自动填充）
- SFTP 传输管理（并发控制、断点续传、时间戳保留）
- SSH Keep-Alive、会话录制、回滚缓冲区管理
- 加密存储（AES 等加密本地数据库）
- WebDAV / S3 加密同步与备份
- 诊断包导出、日志管理

### AI Assistant

NyaTerm 内置 AI 面板，支持：

| 模式 | 说明 |
|------|------|
| **Ask 模式** | 单次问答：生成命令、解释选中输出、分析错误 |
| **Agent 模式** | 多步循环：观察终端输出 → 决策 → 执行 approved 命令 |
| **结构化命令卡片** | 展示风险等级、执行控制，可保存为 Quick Command |
| **终端内联捕获** | Agent 执行时可将输出写回终端，`Terminal Output Lines` 控制显示行数 |
| **会话提及** | 输入 `@` 把其他终端会话纳入 AI 上下文 |
| **Provider 管理** | 内置 provider + 自定义 OpenAI Compatible 端点 |
| **风险控制** | 高影响命令审批门槛 + 更安全的替代建议 |

### SFTP 文件工作流

- 内置 SFTP 文件浏览器，路径跟随终端 cwd
- 传输队列：暂停 / 继续 / 取消 / 重试 / 并发配置
- 本地编辑器打开远程文件 → 保存后 watcher 自动上传回传
- Windows 支持拖拽上传

### 安全与认证

- 密码认证、私钥、主机密钥校验
- 凭据管理（正则匹配终端输出 → 自动填充）
- OTP（TOTP/HOTP、QR 导入、SSH auto-fill）
- 锁屏、主密码保护加密存储
- 代理配置、SSH 跳板机隧道（local/remote/dynamic）

### 同步与备份

- 加密云同步（WebDAV / S3 兼容存储）
- 主密码保护下的加密 `.dgfy` 导入导出
- 快照级冲突解决、启动同步检查、防抖自动推送
- 从 Xshell、MobaXterm、WindTerm 导入会话

## 关键依赖

| 包 | 用途 |
|----|------|
| `@xterm/xterm` + addons | 终端模拟器 |
| `@tauri-apps/api` | Tauri 桥接 |
| `tailwindcss` v4 + shadcn | UI 框架 |
| `i18next` + `react-i18next` | 国际化 |
| `cmdk` | 命令面板 |
| `motion` | UI 动画 |
| `radix-ui` + `class-variance-authority` | 组件系统 |

## 与同类工具的差异

| 工具 | 定位 | AI | SFTP 内嵌 | 加密同步 |
|------|------|-----|---------|---------|
| **NyaTerm** | 现代化多协议终端 + AI | ✅ Agent/Ask 双模式 | ✅ 内嵌浏览器 | ✅ WebDAV/S3 |
| Xshell | 传统 SSH 客户端 | ❌ | ❌ | ❌ |
| MobaXterm | 图形化终端 | ❌ | 基础 | ❌ |
| WindTerm | 开源跨平台终端 | ❌ | 基础 | ❌ |
| Tabby | 开源终端 | ✅ 插件 | ❌ | ❌ |

## 部署与构建

```bash
# 开发
npm run dev          # Vite dev server
npm run tauri dev    # Tauri 窗口

# 构建
npm run build        # tsc + vite build
npm run release      # 全量：version sync → tsc → vite → tauri build
```

构建产物为跨平台原生应用（`.exe` / `.dmg` / AppImage），由 Tauri 打包 Rust 二进制 + Web 前端。

## 总结

NyaTerm 是 **Rust 后端 + React 前端**的现代终端工作区，核心优势在于：AI 辅助（Agent 循环多步执行）、内嵌 SFTP 工作流、加密云同步，以及面向安全和多会话的丰富特性。对比传统 SSH 客户端（Xshell/MobaXterm）和开源方案（Tabby/WindTerm），它在 AI 集成和云同步上有明显差异化优势。