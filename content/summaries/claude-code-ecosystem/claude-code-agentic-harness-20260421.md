---
title: "Claude Code 的 Agentic Harness 架构"
source: code.claude.com/docs/en/how-claude-code-works
date: 2026-04-21
tags: [harness, agentic-loop, tools, context-window, checkpoints, claude-code, anthropic]
summary: Anthropic 官方文档揭示 Agentic Harness 的精确定义：Claude Code 本身即是一个完整的 agentic harness，为 Claude 模型提供工具层、上下文管理层和执行环境。核心是 Agentic Loop 三阶段、Gather Context / Take Action / Verify Results。
---

# Claude Code 的 Agentic Harness 架构

**一句话总结：** Anthropic 官方文档明确指出，Claude Code 本身就是 **agentic harness** 的完整实现——它把一个语言模型变成 capable coding agent 所需的全部基础设施。

## 核心：Agentic Loop

Claude Code 的工作循环分三阶段，循环往复直到任务完成：

```
prompt → Gather Context → Take Action → Verify Results → (loop)
```

- **Gather Context**：读文件、搜代码、理解项目结构
- **Take Action**：编辑代码、运行命令、写新文件
- **Verify Results**：跑测试、比较截图、验证输出

三阶段并非线性串行，而是根据任务自适应——简单问答只需 gather；修复 bug 三个阶段反复循环；重构可能需要 extensive verification。

## Agentic Harness 的精确定义

> **"Claude Code serves as the agentic harness around Claude: it provides the tools, context management, and execution environment that turn a language model into a capable coding agent."**

Harness 包含：
- **Tools**（工具层）：文件操作 / 搜索 / 执行 / Web / 代码智能
- **Context Management**（上下文管理）：Session 持久化、Checkpoint 快照、自动压缩
- **Execution Environment**（执行环境）：本地 / 云端 / 远程控制
- **Safety Mechanisms**（安全机制）：Checkpoint 可逆编辑 + Permissions 权限控制
- **Extension System**（扩展系统）：Skills、MCP、Hooks、Subagents

## 关键设计：Checkpoint

每个文件编辑前 Claude 自动快照，文件变更完全可逆——这是 Claude Code 作为 production-grade harness 的关键设计点，不同于简单的 chat interface。

## 关键设计：Context Window 管理

Context 是核心稀缺资源：
- 自动压缩：先清旧 tool outputs，再 summarize 对话
- 持久化规则 → 放 CLAUDE.md，不依赖对话历史
- `/clear` 主动重置，`/compact` 定向压缩

## 与 Harness Core Concept 的关系

Perplexity 总结的三层架构（约束 / 反馈环 / 质量闸门）在这里得到具体实现：

| Perplexity 三层 | Claude Code 具体实现 |
|----------------|---------------------|
| **约束** | Permissions 权限系统（allowlist / sandbox）|
| **反馈环** | Agentic Loop（Gather → Action → Verify）|
| **质量闸门** | Checkpoint + 测试验证 + Plan Mode |

Anthropic 官方在这里完全印证了 Perplexity 的框架，且给出了具体的工程实现。
