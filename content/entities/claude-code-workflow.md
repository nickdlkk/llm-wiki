---
title: Claude-Code-Workflow
type: entity
created: 2026-05-25
updated: 2026-05-25
sources:
  - https://github.com/catlog22/Claude-Code-Workflow
tags:
  - multi-agent
  - workflow-orchestration
  - claude-code
  - context-engineering
---

> 📄 **原文**：[catlog22/Claude-Code-Workflow](https://github.com/catlog22/Claude-Code-Workflow)

# Claude-Code-Workflow (⭐2034)

**定位**：JSON 驱动的多 Agent 团队协作开发框架——把 Claude Code 从单 Agent 转变为多角色虚拟工程团队。

## 一句话

`npm install -g claude-code-workflow && ccw install -m Global`，即可在项目里获得一个 22 个专业 Agent + 37 个 Skill + 多 CLI 编排的团队开发环境。

## 核心特性

### 22 个专业 Agent

基于事件驱动 beat 模型协调，每个 Agent 有明确职责（team-worker、cli-discuss 等），通过消息总线协议通信。

### 多 CLI 编排

不止 Claude——自动选择或手动指定 Gemini/Qwen/Codex/Claude，支持四种协同模式：

| 模式 | 说明 |
|------|------|
| Collaborative | 多模型协作同一任务 |
| Parallel | 多模型并行执行，对比结果 |
| Iterative | 模型间互相迭代改进 |
| Pipeline | 链式传递，前一步输出作为下一步输入 |

### 37 个 Skill

按功能分组：

- **规划类**：`workflow-lite-plan`（轻量）、`brainstorm`（多角色分析）、`workflow-plan`、`workflow-tdd-plan`
- **执行类**：`workflow-test-fix`、team-* 系列
- **管理类**：issue 管理队列、memory 记忆管理

### Wisdom 积累系统

跨会话持久化 learnings/decisions/conventions，让团队 Agent 在新会话中继承历史经验——这是 Self-Refinement 的工程实现。

### 可视化工具

- **Terminal Dashboard**：多终端网格 + 执行监控 + 文件侧边栏 + 会话分组
- **Orchestrator Editor**：React Flow 拖拽式可视化工作流模板编辑器
- **A2UI**：Agent-to-User 交互界面，实时 Agent 通信

### codex-lens（ACE）

本地语义代码搜索引擎（Augment Context Engine），为 Agent 提供代码上下文检索能力。

## 项目结构

```
Claude-Code-Workflow/
├── .claude/
│   ├── agents/          # 22 个专业 Agent
│   ├── commands/        # 5 类命令：ccw, cli, issue, memory, workflow
│   └── skills/          # 37 个模块化 Skill
├── ccw/
│   ├── src/             # TypeScript 源码（commands, core, mcp-server, tools）
│   └── frontend/        # React 前端（Terminal Dashboard, Orchestrator）
├── codex-lens/          # 本地语义代码搜索引擎
└── docs/
```

## 与同类方案对比

| | Claude-Code-Workflow | gstack | OneRedOak workflows |
|---|---|---|---|
| **定位** | 多 Agent 团队编排 | 单人多角色 | Review 三件套 |
| **多模型** | ✅ Gemini/Qwen/Codex/Claude | ❌ | ❌ |
| **Agent 数** | 22 | ~33 skills | 3 workflows |
| **复杂度** | 重（完整框架） | 中（skill 包） | 轻（18 文件） |
| **可视化** | Terminal Dashboard + React 编辑器 | 浏览器 daemon | ❌ |
| **跨会话记忆** | Wisdom 积累系统 | learnings.jsonl | ❌ |
| **适用场景** | 大团队多 Agent 协作 | 个人/小团队 | PR 自动 review |

## 五条方法论映射

| 本文方法论 | Claude-Code-Workflow 的实现 |
|-----------|--------------------------|
| **依赖倒置** | CLI 编排层抽象，底层 Gemini/Qwen/Codex/Claude 可替换 |
| **零侵入隔离** | `.claude/` 目录隔离，不影响项目原有结构 |
| **工具 AI 化** | codex-lens 本地语义搜索 + MCP Server 能力暴露 |
| **Self-Refinement** | Wisdom 积累系统：learnings/decisions/conventions 跨会话沉淀 |
| **分层验证** | TDD workflow + 门禁式 release pipeline |
