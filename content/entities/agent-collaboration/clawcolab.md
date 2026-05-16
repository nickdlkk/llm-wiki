---
title: ClawColab
description: Secure GitHub-native collaboration workspace for multiple OpenClaw instances — approval gates、secrecy isolation、structured multi-agent coordination
github: https://github.com/virtual-ny/ClawColab
stars: 1
tags: [openclaw, github-native, multi-agent, collaboration, approval-gates, open-source]
created: 2026-05-16
---

# ClawColab

## 概述

**ClawColab** 是一个 GitHub 原生的多 OpenClaw 实例协作协议和工作空间模式，专为半信任团队协作设计。Agent 可以协调、提议工作、交接上下文、共享已批准 artifacts，而不会将仓库变成私密记忆或 secrets 的倾倒场。

**GitHub**: [virtual-ny/ClawColab](https://github.com/virtual-ny/ClawColab)  
**Language**: Python  
**License**: None  
**Stars**: 1  

## 核心理念

> **半信任团队协作 (Half-trust teamwork)**
> 
> Agent 可以结构化地分工、提 proposal、留 decision、写 risk 和 handoff，同时默认隔离私密记忆和敏感信息。

## 核心功能

| 功能 | 描述 |
|------|------|
| Shared Coordination | 多 OpenClaw 实例共享协调 |
| Approval-aware Workflows | 任务和提案工作流带审批 |
| Structured Artifacts | decisions, risks, claims, handoffs |
| Secrecy Boundaries | `private`, `sealed`, `shared-team`, `public-repo` |
| Demo Spaces | 项目特定演示空间托管模式 |

## 仓库布局

```
ClawColab/
├── workspace/                    # 活跃协调区
├── workspace/demo-spaces/        # 嵌入式项目/演示空间
├── templates/minimal-start/     # 最小启动模板
├── onboarding/                  # 第二Agent onboarding材料
├── docs/                        # 支持文档
├── archive/history/             # 历史trial artifacts
└── sealed/INDEX.md             # 机密参考索引
```

## 快速开始

1. 为一个明确的协作边界创建一个 ClawColab 仓库
2. 定义参与者和人工审批人
3. 如有需要，在 `workspace/demo-spaces/<project-name>/` 下创建项目空间
4. 先只使用三个对象：`task`、`proposal`、`decision`
5. 先看 `QUICKSTART.md`，并优先使用 `templates/minimal-start/`

## 安全边界类型

| 类型 | 描述 |
|------|------|
| `private` | 完全私密，仅创建者可见 |
| `sealed` | 密封，需解密才可访问 |
| `shared-team` | 团队共享 |
| `public-repo` | 公开仓库 |

## 与其他工具对比

| 工具 | 定位 | 协作模式 |
|------|------|----------|
| ClawColab | GitHub原生协作 | Structured tasks + approvals |
| ClawPort | OpenClaw仪表盘 | UI dashboard |
| AgentVerse | 多Agent讨论 | 群聊 + 角色 |
| Vibe Kanban | AI代码执行 | 看板 + Git worktree |

## 相关链接

- [GitHub Repository](https://github.com/virtual-ny/ClawColab)
