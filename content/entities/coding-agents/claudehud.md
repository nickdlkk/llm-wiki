---
title: ClaudeHUD
description: Claude Code 状态行插件，实时显示项目路径、上下文健康度、工具活动、Agent 跟踪和 Todo 进度
github: unknown
stars: 0
tags: [Claude Code, 状态行, 开发者工具, coding-agents]
created: 2026-05-15
---

# ClaudeHUD

## 简介
ClaudeHUD 是一款专为 Claude Code 打造的状态行（status bar）插件，利用 Claude Code 原生的 `statusline` 接口，在编辑器底部实时显示项目关键信息。它帮助开发者无需切换上下文即可感知 Agent 运行状态、项目健康度和任务进度。

## 核心功能
- **项目路径显示**：当前工作目录和项目名称一目了然
- **上下文健康度可视化**：以颜色或图标直观呈现上下文窗口使用率
- **工具活动跟踪**：实时展示 Agent 当前调用的工具（Search/Read/Write/Bash 等）
- **Agent 跟踪**：显示当前任务执行的 Agent 链路和层级
- **Todo 进度展示**：内联呈现任务清单完成状态

## 优缺点
**优点：** 实时状态感知；上下文健康度可视化；无侵入性，依托 Claude Code 原生能力

**缺点：** 仅限 Claude Code 使用；状态行空间有限，信息密度受屏幕宽度制约

## 技术规格
- 语言/框架：基于 Claude Code 原生 statusline 扩展机制
- 许可证：unknown
- Stars：0

## 相关链接
- GitHub: unknown
