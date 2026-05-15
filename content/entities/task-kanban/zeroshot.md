---
title: zeroshot
tags:
  - AI-Agent
  - JavaScript
  - CLI
  - Multi-Agent
  - Autonomous-Engineering
  - MIT
  - Claude-Code
  - Codex
  - Gemini
---

# zeroshot

> **Repo**: [covibes/zeroshot](https://github.com/covibes/zeroshot)
> **NPM**: [@covibes/zeroshot](https://www.npmjs.com/package/@covibes/zeroshot)
> **License**: MIT
> **Language**: JavaScript/Node.js 18+
> **Stars**: 1.5k
> **定位**: 自主工程团队 CLI — 多 Agent 编排实现、审查、验证闭环

## 核心定位

zeroshot 是一个**开源 AI Coding Agent 编排 CLI**，将多个 Agent（Planner、Implementer、Validator）串联成工作流，自主实现、审查、测试并验证代码变更，直到通过或返回可操作的失败原因。

设计理念：**正确性 > 速度**。

## 核心架构：Planner + Implementer + Validators

```
zeroshot run <issue>
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│  1. PLANNER — 将任务翻译为具体验收标准                         │
│     "Add rate limiting: sliding window, per-IP, 429"         │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│  2. IMPLEMENTER — 在隔离环境实现变更                          │
│     (local / git worktree / Docker)                         │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│  3. VALIDATORS (blind) — 独立验证器运行自动化检查              │
│     • 测试验证 (测试通过?)                                    │
│     • 代码审查 (lint/类型检查?)                              │
│     • 功能验证 (行为符合 spec?)                               │
└─────────────────────────────────────────────────────────────┘
    │
    ├── 验证通过 → 完成
    │
    └── 验证失败 → 迭代修复 → 重新验证 → 循环直到通过或放弃
```

### Blind Validation 机制

**关键创新**：Validator 看不到 Implementer 的上下文和代码历史，强制独立验证。

| | 普通 Agent 自测 | zeroshot blind validation |
|---|---|---|
| 验证独立性 | ❌ 自己写自己测 | ✅ 独立验证器 |
| 上下文污染 | ⚠️ 可能记住方案 | ❌ 看不到实现细节 |
| 验证质量 | 取决于 Agent 自觉 | 结构化强制检查 |

## 多 Agent 迭代循环

```
实现 → 验证 → 失败 → 修复 → 验证 → ... → 通过
           ↓
    返回可操作的失败原因（而非模糊反馈）
```

zeroshot 会在每次验证失败后给出**可复现的失败**信息，直到代码真正通过验证或被明确拒绝。

## 支持的 Provider

| Provider | CLI 包 | 说明 |
|----------|--------|------|
| **Claude Code** | @anthropic-ai/claude-code | Anthropic 模型 |
| **OpenAI Codex** | @openai/codex | OpenAI 模型 |
| **Gemini CLI** | @google/gemini-cli | Google 模型 |
| **OpenCode** | opencode.ai | 开源选项 |

默认 provider 可全局设置，也可单次 run 时覆盖：
```bash
zeroshot providers set-default codex
zeroshot run 123 --provider gemini
```

## 支持的 Issue 后端

| Backend | 用法 |
|---------|------|
| **GitHub** | `zeroshot run 123` (issue 编号) |
| **GitLab** | `zeroshot run gitlab:123` |
| **Jira** | `zeroshot run jira:PROJECT-123` |
| **Azure DevOps** | `zeroshot run azure:123` |
| **Markdown 文件** | `zeroshot run feature.md` |
| **内联文本** | `zeroshot run "Add dark mode"` |

## 隔离模式

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| **None** | 直接在当前目录修改 | 简单任务、实验 |
| **Git Worktree** | 独立分支工作区 | 安全审查 |
| **Docker** | 容器内隔离执行 | 高风险变更、CI |

## Crash Recovery

所有状态持久化到 SQLite，支持随时恢复：
```bash
zeroshot resume <session-id>
```

## 适用场景

| 场景 | 适用性 | 原因 |
|------|--------|------|
| 添加限流（滑动窗口、per-IP、429） | ✅ | 需求明确 |
| 重构 auth 为 JWT | ✅ | 有定义的终态 |
| 修复 login bug | ✅ | 成功可衡量 |
| 修复 2410 个 lint 违规 | ✅ | 明确的完成标准 |
| 让应用更快 | ❌ | 需要探索 |
| 改善代码库 | ❌ | 无验收标准 |
| 排查 flaky tests | ❌ | 探索性任务 |

**经验法则**：如果无法描述"完成"是什么样子，validator 无法验证它。

## 与其他方案对比

| | zeroshot | OpenHands | Coder Studio |
|---|---|---|---|
| **核心** | 多 Agent 验证循环 | 端到端平台 | 监督 IDE |
| **Validator** | Blind validation | 无 | 无 |
| **隔离** | None/Worktree/Docker | Docker | None |
| **Provider** | Claude/Codex/Gemini/OpenCode | 任意（litellm） | 任意 |
| **License** | MIT | MIT | MIT |
| **State** | SQLite 持久化 | SQLite | Session |

## 安装

```bash
# NPM 全局安装
npm install -g @covibes/zeroshot

# 安装 Provider CLIs（至少一个）
npm i -g @anthropic-ai/claude-code
npm i -g @openai/codex
npm i -g @google/gemini-cli

# 认证
claude login        # Claude
codex login         # Codex
gemini auth login   # Gemini

# GitHub 认证（用于 issue 编号）
gh auth login
```

## 常用命令

```bash
zeroshot run 123                    # GitHub issue
zeroshot run feature.md             # Markdown 文件
zeroshot run "Add dark mode"        # 内联文本
zeroshot providers                  # 查看/设置 provider
zeroshot resume <session-id>        # 恢复会话
```

## 适用人群

- 重视正确性和可复现性的资深工程师
- 自动化 PR 工作流和代码审查的团队
- 标准化 Agent 工作流的 Infra/Platform 团队
- 处理 issue backlog 的开源维护者
- 需要验证而非"感觉对"的 AI 高级用户

## 参考

- [zeroshot Documentation](https://github.com/covibes/zeroshot)
- [Discord Community](https://discord.gg/PdZ3UEXB)
