---
title: EasyCodex
type: entity
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://github.com/Ryan-Laws/easycodex
  - https://github.com/Ryan-Laws/easycodex/releases/tag/v0.1.7
tags:
  - codingagent
  - codex
  - mobile-control
  - relay
  - cross-platform
---

# EasyCodex

**A local-first mobile and desktop control room for Codex agents.**

GitHub: [Ryan-Laws/easycodex](https://github.com/Ryan-Laws/easycodex) · ⭐ 15 · Kotlin · MIT

## 一句话定位

Relay 运行在电脑侧，手机扫码配对远程控制 Codex 工作流。数据路径：手机 → 本地 relay WebSocket，代码/凭证不离开本机。

## 架构

- **Relay** (桌面端)：启动并监控 `codex app-server`，暴露安全 QR/深链接配对，转发手机端指令
- **手机端**：连接 relay WebSocket，不运行 Codex，只做远程控制台

## 与 OpenAI Codex Mobile Preview 的区别

| 维度 | EasyCodex | OpenAI Codex Mobile Preview |
|---|---|---|
| 桌面平台 | Win/macOS/Linux relay | macOS（Win 即将支持） |
| 配对方式 | QR/深链接配对自建 relay | ChatGPT mobile app 内 |
| Provider 灵活性 | 跟 host 上的 Codex 配置走，兼容非 OpenAI | 绑定 OpenAI 账号 |
| 数据路径 | 手机 ↔ 本地 relay，本地网络 | OpenAI 中转基础设施 |

## 功能清单

- 创建/恢复/中断/停止/归档 Codex 任务
- 实时会话流更新
- 审批确认和用户输入提示
- 发送文本、图片、文件附件
- 计划审查（执行前预览 Codex 计划）
- Git status/diff 查看 + 选择性提交
- 项目和工作树浏览
- 多窗口 Codex CLI 控制台
- 移动端快速回复/emoji/语音输入
- 通知推送 + 更新检测
- 任务列表同步

## v0.1.7 更新内容（2026-05-18）

- 跨平台桌面 relay workspace 发现机制：首次启动可指定项目文件夹
- Relay 和桌面 workspace 安全测试
- Android APK 更新链测试
- 修复：桌面 relay 启动阻塞（stale default workspace 指向 home 目录）
- 强化：home/Desktop/Documents/Downloads 等目录 realpath 解析后仍被拒绝

## 安装

| 平台 | 下载 |
|---|---|
| Windows | `EasyCodex.Relay.Setup.*-x64.exe`（推荐）或 Portable |
| Android | `EasyCodex.Mobile.*.apk` |
| macOS | `EasyCodex.Relay.*.mac-arm64.dmg` / `.mac-x64.dmg` |
| Linux | `EasyCodex.Relay.*.linux-x64.AppImage` / `.deb` |

## 快速上手

1. 安装 Codex CLI 并完成认证
2. 安装并打开 **EasyCodex Relay** 桌面端
3. 选择默认 workspace，启动 relay
4. Android 手机安装 `EasyCodex.Mobile.*.apk`
5. 扫描 QR 码或打开 `easycodex://connect` 深链接
6. 选择 workspace，创建或恢复任务
