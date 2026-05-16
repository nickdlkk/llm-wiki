---
title: "Anthropic Agent SDK 架构解析"
source: docs.anthropic.com/en/docs/agent-sdk/overview
date: 2026-04-21
tags: [agent-sdk, python, typescript, hooks, subagents, mcp, anthropic]
summary: Anthropic Agent SDK 将 Claude Code 的全部能力抽象为 Python/TypeScript SDK，包含 Tools、Hooks、Subagents、MCP、Permissions、Sessions 六大组件。与 Client SDK 的根本区别在于：Client SDK 需要自己实现 tool loop，Agent SDK 由 Claude 接管。
---

# Anthropic Agent SDK 架构解析

**一句话总结：** Agent SDK 把 Claude Code 的整个 agentic harness 封装成 Python/TypeScript 库，让开发者可以在自己的应用里编程式使用相同的工具链、agent loop 和上下文管理能力。

## Agent SDK vs Client SDK

这是理解整个 SDK 设计的关键：

| | **Client SDK** | **Agent SDK** |
|---|---|---|
| 你做什么 | 自己实现 tool loop | Claude 接管 tool loop |
| 控制权 | 你控制每个工具调用 | SDK 内置 agentic loop |
| 适用场景 | 简单 API 调用 | 复杂 autonomous 任务 |
| 代码量 | 多（需要自己搭 loop） | 少（SDK 负责循环）|

Client SDK = 你在开车，SDK 是引擎
Agent SDK = SDK 在开车，你设定目的地

## 六大 Harness 组件

### 1. Tools（内置工具）

覆盖 agent 工作的全部基础能力：

| Tool | 作用 |
|------|------|
| Read/Write/Edit | 文件操作 |
| Bash | 终端命令、git、脚本 |
| Monitor | 监听后台脚本输出，事件驱动响应 |
| Glob/Grep | 代码库搜索 |
| WebSearch/WebFetch | Web 查询 |
| AskUserQuestion | 向用户提问（多选）|

### 2. Hooks（生命周期钩子）

在 agent 运行的关键节点插入自定义逻辑：

```python
# 可用钩子
PreToolUse    # 工具执行前
PostToolUse   # 工具执行后
Stop          # agent 停止时
SessionStart  # 会话开始时
SessionEnd    # 会话结束时
UserPromptSubmit  # 用户提交 prompt 时
```

典型用途：日志审计、敏感操作拦截、自动 lint、文件变更记录。

### 3. Subagents（子 Agent）

在独立上下文里 spawn 专用 agent，主 agent 接收汇总结果。

关键字段：`parent_tool_use_id`——追踪哪个子 agent 产出了哪个消息。

### 4. MCP（Model Context Protocol）

外部工具接入标准协议。与 Claude Code 的 MCP Server 连接相同，允许接入 Notion、Figma、数据库等外部服务。

### 5. Permissions（权限系统）

精确控制每个工具的可用性：
- 预批准安全操作（Read/Glob/Grep 等只读工具）
- 阻止危险操作（rm -rf、网络请求等）
- 要求审批（特定操作需要人工确认）

### 6. Sessions（会话管理）

跨多次交互维持上下文：
- 记忆文件读取、分析结果、对话历史
- 支持 resume（继续同一会话）和 fork（分叉探索不同路径）

## 架构图

```
Agent = Model (reasoning) + Harness (everything else)

Harness components:
├── Tools (Read, Write, Edit, Bash, Monitor, Glob, Grep, Web, Ask)
├── Hooks (PreToolUse, PostToolUse, Stop, SessionStart/End...)
├── Subagents (spawn with isolated context)
├── MCP (external tools protocol)
├── Permissions (allowlist/blocklist per tool)
├── Sessions (persist across exchanges)
└── Filesystem config (CLAUDE.md, skills, commands)
```

## 实践意义

Anthropic 的设计验证了 Harness Engineering 的核心命题：**Model 只是"脑子"，真正的工程复杂度在 Harness**。SDK 的六大组件对应了 Perplexity 框架的每一层：

- **约束层** → Permissions + Hooks
- **反馈环** → Agent Loop + Tools + Sessions
- **质量闸门** → 内置验证 + 自定义 Hooks 质量检查

这个 SDK 本身就是一份 Harness Engineering 的权威实现参考。
