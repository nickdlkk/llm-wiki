---
title: Trellis
type: entity
source: github:mindfold-ai/Trellis
created: 2026-04-17
updated: 2026-04-17
tags:
  - multi-platform
  - spec-injection
  - git-worktree
  - task-workflow
  - claude-code
  - cursor
  - codex
  - nodejs
  - python
---

# Trellis

*"A multi-platform AI coding framework that rules."*

**Repo:** `mindfold-ai/Trellis` | **Lang:** Node.js + Python | **License:** AGPL-3.0 | **Status:** Active | **Platforms:** 14

---

## Summary

Trellis 是一个**多平台 AI 编码框架**，核心解决"规范复用"问题。在 `.trellis/spec/` 中写一次规范，该规范就会自动注入到每个 AI 会话中，无需每次重复粘贴指令。

**最独特的设计：**
- 一次 `trellis init` 可以同时为 14 个 AI 平台生成集成文件
- 所有平台共享同一套 `.trellis/spec/` 和 `.trellis/tasks/`
- 基于 Anthropic 文章 [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) 构建

---

## Architecture

```
.trellis/
├── spec/                    # 项目标准、规范、模式指南（MUST READ before coding）
│   └── <package>/
│       └── <layer>/         # e.g., backend/, frontend/, unit-test/
│           └── index.md      # Pre-Dev Checklist + Quality Check
├── tasks/                   # Task PRD、上下文文件、状态
│   └── {MM-DD-name}/
│       └── task.json
├── workspace/               # 开发者日志和会话连续性
│   └── {developer}/
│       ├── index.md
│       └── journal-N.md
├── scripts/                 # Python 工具脚本
│   ├── get_context.py       # 统一上下文获取
│   ├── add_session.py       # 会话记录
│   ├── task.py              # Task 管理
│   ├── init_developer.py   # 开发者身份初始化
│   └── multi_agent/         # 多 Agent pipeline 脚本
└── workflow.md              # 共享工作流规则

# 根据启用平台，trellis init 还会生成：
.claude/, .cursor/, .codex/, .kilocode/, .kiro/skills/,
.gemini/, .agent/workflows/, .windsurf/workflows/, .qoder/,
.codebuddy/, .github/copilot/, .agents/skills/ 等
```

---

## Core Problem Solved

**规范不落地的根本问题：** CLAUDE.md / AGENTS.md / .cursorrules 容易变成"大而全"的 monolith，新会话时 AI 无法有效获取所需上下文。

Trellis 的方案：
- 分层规范（`spec/<package>/<layer>/index.md`）
- Task 上下文自动注入（`.trellis/.current-task`）
- Workspace 日志跨会话记忆
- 自动上下文发现（`get_context.py --mode packages`）

---

## Slash Commands

| Command | Function |
|---------|----------|
| `/start` | 加载项目上下文到会话（自动 hook 触发） |
| `/brainstorm` | 需求评审，生成 PRD |
| `/before-dev` | 读取相关规范 before coding（自动检测前端/后端）|
| `/check` | 对照规范 review + 自动修复违规（自动检测前端/后端）|
| `/finish-work` | Pre-commit checklist（lint + tests + docs + API changes）|
| `/parallel` | 在隔离 git worktree 中启动多个并行 Agent |
| `/record-session` | 保存会话摘要到 workspace journal |
| `/update-spec` | 将新模式/规范捕获到 spec 文件 |

---

## Multi-Platform Support

14 个平台：**Claude Code, Cursor, OpenCode, iFlow, Codex, Kilo, Kiro, Gemini CLI, Antigravity, Windsurf, Qoder, CodeBuddy, GitHub Copilot, Factory Droid**

所有平台共享同一套 `.trellis/spec/` 和 `.trellis/tasks/`，一个会话中建立的规范对所有平台可用。

---

## Key Design

### Layered Spec System

```
.trellis/spec/
├── <package>/          # e.g., cli/, docs-site/
│   └── <layer>/       # e.g., backend/, frontend/, unit-test/
│       ├── index.md   # Start here — Pre-Dev Checklist + Quality Check
│       └── *.md       # Topic-specific guideline docs
└── guides/            # Cross-package thinking guides (always read)
```

`get_context.py --mode packages` 自动发现所有包和层，生成 Pre-Dev Checklist。

### Task Continuity

`task.py start <name>` 写入 `.trellis/.current-task`，新会话自动在上下文中注入 `## CURRENT TASK`，无需手动告知 AI 在做什么。

### Parallel via Git Worktrees

`/parallel` 在隔离 git worktree 中运行多 Agent，防止分支冲突。每个 Agent 有独立分支、独立工作区，完成后各自开 draft PR。

### Workspace Journals

`.trellis/workspace/{developer}/journal-N.md` 记录每次会话的工作内容，每个日志最多 2000 行，超出自动创建新文件。新会话启动时 hook 自动读取历史日志。

---

## vs Other Tools

| Aspect | Trellis | OpenSpec | Vibe-Skills |
|--------|---------|----------|-------------|
| Core concept | Spec injection + task workflow | Spec delta + artifact-driven | Governed runtime + 340+ skills |
| Entry point | Slash commands (`/start`, `/brainstorm`...) | `/opsx:propose` | `/vibe` 6-stage state machine |
| Spec format | Markdown layered by package/layer | Markdown + delta diffs | SKILL.md conventions |
| Multi-platform | 14 platforms | 26 tool adapters | 6+ hosts |
| Multi-agent | Git worktree parallel | Adapters (no native) | Root/Child lanes |
| Memory | Workspace journals | Profile/skill | 4-layer (session/project/task/knowledge) |
| Governance | Specs + checklists | Delta tracking | 129 governance rules |

---

## Strengths

- **最广泛的多平台支持** — 14 个平台同一套规范，开源项目利器
- **规范注入而非规范堆砌** — 分层结构解决 monolith 规范问题
- **基于 Anthropic 研究** — 理论基础扎实（Effective Harnesses for Long-Running Agents）
- **Task continuity** — `.current-task` 机制确保会话间任务不丢失
- **Workspace journals** — 开发者日志体系完善，支持多开发者
- **活跃开发** — v0.4.0 持续演进

## Weaknesses

- Node.js 依赖（Python 仅用于 hooks/scripts）
- 多开发者 workspace 日志可能产生冲突（需要 git 协调）
- 规范需要手动维护，对于小型项目 overhead 较高
- AGPL-3.0 许可证可能限制商业使用
