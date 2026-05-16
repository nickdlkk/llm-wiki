---
title: Aperant (Auto Claude)
description: Autonomous multi-session AI coding — 自主多会话AI编码框架，支持并行执行、隔离工作区、自我验证QA
github: https://github.com/AndyMik90/Aperant
stars: 14249
tags: [autonomous-coding, multi-agent, claude-code, ai-agents, open-source]
created: 2026-05-16
---

# Aperant (Auto Claude)

## 概述

**Aperant** (原名 Auto Claude) 是一个自主多会话AI编码框架，核心理念：**描述你的目标，Agent自动处理规划、实施和验证**。支持并行执行（最多12个Agent终端）、隔离工作区（Git worktree）、自我验证QA。

**GitHub**: [AndyMik90/Aperant](https://github.com/AndyMik90/Aperant)  
**官网**: [aperant.com](https://aperant.com)  
**Language**: TypeScript  
**License**: AGPL-3.0  
**Stars**: 14,249 ⭐  
**Forks**: 1,929  

## 核心功能

| 功能 | 描述 |
|------|------|
| Autonomous Tasks | 描述目标，Agent自动规划、实现、验证 |
| Parallel Execution | 最多12个Agent终端并行执行 |
| Isolated Workspaces | Git worktree隔离，主分支始终安全 |
| Self-Validating QA | 内置质量保证环，提交前catch问题 |
| AI-Powered Merge | 自动解决合并冲突 |
| Memory Layer | Agent跨session保留记忆 |
| GitHub/GitLab集成 | 导入issue、AI调查、创建MR |
| Linear集成 | 同步任务到Linear跟踪进度 |
| Cross-Platform | Windows/macOS/Linux原生桌面应用 |
| Auto-Updates | 自动更新 |

## 系统要求

- **Claude Pro/Max订阅**
- **Claude Code CLI**: `npm install -g @anthropic-ai/claude-code`
- **Git仓库**: 项目必须初始化为git repo

## 界面

### Kanban Board

可视化任务管理，从规划到完成的整个流程。创建任务并实时监控Agent进度。

### Agent Terminals

AI驱动的终端，一键注入任务上下文。生成多个Agent并行工作。

## 技术架构

```
Aperant (Auto Claude)
├── Claude Code CLI (Agent执行)
├── Git Worktree (隔离工作区)
├── Kanban Board (任务管理)
├── Parallel Execution Engine (12并发)
├── Self-Validating QA Loop
└── Memory Layer (跨session记忆)
```

## 与其他工具对比

| 工具 | 定位 | 自动化程度 |
|------|------|------------|
| Aperant | 端到端自主编码 | 全自动化 |
| Vibe Kanban | AI执行+Human Review | 半自动 |
| ClawPort | OpenClaw可视化 | 监控为主 |
| AgentVerse | 多Agent讨论 | 协作决策 |

## 相关链接

- [GitHub Repository](https://github.com/AndyMik90/Aperant)
- [官网](https://aperant.com)
- [Discord社区](https://discord.gg/KCXaPBr4Dj)
- [YouTube频道](https://www.youtube.com/@AndreMikalsen)
