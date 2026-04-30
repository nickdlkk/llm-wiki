---
title: learn-claude-code
type: entity
created: 2026-04-16
updated: 2026-04-16
sources:
  - https://github.com/shareAI-lab/learn-claude-code
tags:
  - course
  - claude-code
  - harness
---

# learn-claude-code

> Bash is all you need — A nano claude code–like「agent harness」，built from 0 to 1

## 基本信息

- **Stars**: 53,997 ⭐
- **语言**: TypeScript
- **URL**: https://github.com/shareAI-lab/learn-claude-code
- **Created**: 2025-06-29

## 课程结构（12 sessions）

每个 session 添加一个 harness 机制，每个机制有一句 motto：

| Session | Motto | 添加的机制 |
|---|---|---|
| s01 | "One loop & Bash is all you need" | 一个 tool + 一个 loop = agent |
| s02 | "Adding a tool means adding one handler" | loop 不变，新工具注册到 dispatch map |
| s03 | "An agent without a plan drifts" | 先列步骤再执行，完成率翻倍 |
| s04 | "Break big tasks down" | subagent 用独立 messages[]，保持主对话干净 |
| s05 | "Load knowledge when you need it" | 通过 tool_result 注入，不是 system prompt |
| s06 | "Context will fill up" | 三层压缩策略，无限会话 |
| s07 | "Break big goals into tasks, order, persist" | 基于文件的 task graph，依赖关系 |
| s08 | "Run slow ops in background" | daemon 线程运行命令，完成时注入通知 |
| s09 | "Delegate to teammates" | 持久化 teammates + 异步 mailbox |
| s10 | "Teammates need shared communication rules" | 一个 request-response 模式驱动所有协商 |
| s11 | "Teammates scan the board and claim tasks" | 不需要 lead 逐个分配 |
| s12 | "Each works in its own directory" | worktree 隔离，无干扰 |

## 核心命题

**Agency comes from the model. An agent product = Model + Harness.**

- 模型提供智能（driver）
- Harness 提供环境（vehicle）

Claude Code 的本质：
```
Claude Code = one agent loop
            + tools (bash, read, write, edit, glob, grep, browser...)
            + on-demand skill loading
            + context compression
            + subagent spawning
            + task system with dependency graph
            + team coordination with async mailboxes
            + worktree isolation for parallel execution
            + permission governance
```

## 关于"不是 agent"的澄清

Prompt plumbing（拖拽 workflow 构建器、no-code AI agent 平台）不是真正的 agent：
- 它们是"在 procedural 规则上套 LLM"的 Rube Goldberg machine
- 不是 agent，是"带 LLM 味的 shell 脚本"
- agency 是训练出来的，不是编程编出来的

## 参见

- [[Harness Engineering]] — learn-claude-code 完整演示了 Harness 工程的所有方面
