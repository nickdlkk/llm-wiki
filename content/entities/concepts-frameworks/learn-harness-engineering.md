---
title: learn-harness-engineering
type: entity
created: 2026-04-16
updated: 2026-04-16
sources:
  - https://github.com/walkinglabs/learn-harness-engineering
tags:
  - course
  - harness
---

# learn-harness-engineering

> Harness Engineering 官方风格入门教程，from 0 to 1

## 基本信息

- **Stars**: 752
- **语言**: TypeScript
- **URL**: https://github.com/walkinglabs/learn-harness-engineering
- **Created**: 2026-03-29
- **官方文档**: https://walkinglabs.github.io/learn-harness-engineering/

## 课程大纲

课程分为三个主要部分：

1. **讲义 (Lectures)**: 12 个概念单元，深度解析 Harness 设计的理论基础
2. **项目 (Projects)**: 6 个循序渐进的实战项目，从零搭建可靠的 Agent 工作环境
3. **资料库 (Resource Library)**: 开箱即用的中英文模板（AGENTS.md、feature_list.json、init.sh 等）

## 课程特色

贯穿项目：**一个基于 Electron 的个人知识库桌面应用**，6 个课程项目都围绕同一个产品。

课程代码结构：
- `docs/lectures/` — 12 个讲义（每个含 `index.md` + `code/` 示例）
- `projects/shared/` — 共享的 Electron + TypeScript + React 基础
- `projects/project-NN/starter|solution/` — 每个项目的起始/完整代码

## 快速开始

不需要读完 12 个讲义再动手。最小起步四个文件：

```
项目根目录/
├── AGENTS.md              ← agent 的操作手册
├── init.sh                ← 一条命令完成安装 + 验证 + 启动
├── feature_list.json      ← 有哪些功能、哪些已完成
└── progress.md            ← 每次会话做了什么
```

## 参考资料

- [OpenAI: Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)
- [Anthropic: Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Anthropic: Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [Awesome Harness Engineering](https://github.com/walkinglabs/awesome-harness-engineering)

## 参见

- [[Harness Engineering]] — 课程核心理论
- [[learn-claude-code]] — 同一作者，更偏重 Claude Code 生态
