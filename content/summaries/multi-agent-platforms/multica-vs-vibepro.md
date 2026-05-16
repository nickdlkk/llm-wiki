---
title: Multica vs VibeKanBan 对比
description: 多 Agent 任务平台横向对比：Multica 26k stars vs VibeKanBan，聚焦代码浏览、Git 分支管理、UI 体验
created: 2026-05-09
updated: 2026-05-09
sources:
  - /root/llm-wiki/wiki/entities/Multica.md
  - /root/llm-wiki/wiki/concepts/se-and-llm-harness.md
tags:
  - AI Agent
  - 任务管理
  - Kanban
  - 多 Agent 协作
---

# Multica vs VibeKanBan 对比

**Type:** Tool Comparison · AI Coding Agent Task Management Platforms
**Date:** 2026-05-09
**Context:** 刚刚完成 Multica 自托管部署实测（`~/.multica/server/`），结合 wiki 中 PAS AI 工具选型记录

---

## 一句话结论

> Multica 是**面向 Agent 的后端调度系统**，VibeKanBan 是**面向人的看板界面**。定位不同，导致体验差距大。

---

## 功能对比

| 维度 | Multica | VibeKanBan |
|------|---------|------------|
| **定位** | Agent 任务调度 + 生命周期管理 | 人类看代码 / 审 agent 产物的看板 |
| **Stars** | 26,275 ⭐ | 未详（与 CodeKanban、Cline Kanban 同级） |
| **技术栈** | Go 后端 + Next.js 前端 + PostgreSQL + pgvector | 未详 |
| **自托管** | ✅ Docker Compose，一行部署 | 未确认 |
| **Agent CLI 支持** | 11 种（Claude Code、Codex、Copilot、OpenClaw、OpenCode、**Hermes**、Gemini、Pi、Cursor、Kimi、Kiro） | 未详 |
| **Git 分支管理** | Git worktree 隔离（底层扎实），但 UI 只支持 `--default-branch-hint` CLI 指定 | 未详 |
| **文件浏览器** | ❌ 无内置 UI，只能 `docker exec` 进容器或 SSH 到 worktree 目录看 | 未详 |
| **Diff 查看** | ✅ `BranchesWidget` 展示变更文件列表 + `BranchChanges`（git diff 输出） | ✅ 看板式 review（推测） |
| **产物管理** | `FilesProduced` 字段记录产物列表 | 未详 |
| **邮件验证** | 需要 RESEND_API_KEY；无 key 时验证码打在容器日志里 | N/A |
| **UI 体验** | 功能能用但粗糙，代码浏览是明显短板 | 未实测 |

---

## 核心差距分析

### 1. Multica 的文件浏览缺陷

这是 Nick 最不满的一点。实测确认：

- **后端有数据**：TaskResult 包含 `BranchChanges`（git diff）、`BranchName`、`FilesProduced`
- **前端没有对应的文件浏览器**——BranchesWidget 只展示"有哪些变更"，不能点开某个文件看内容
- **临时方案**：SSH 进 `~/.multica/workspace/<issue-id>/` 直接读文件，或者让 agent 在回复里 `cat` 文件内容

**结论**：Multica 是给 agent 用、不是给人用的。人想看代码还得另开终端。

### 2. VibeKanBan 的定位

根据 wiki 记录，VibeKanBan 和 CodeKanban、Cline Kanban 并列，属于「任务看板」类别：

> "单需求不需要，多需求时人成为瓶颈"

VibeKanBan 的核心价值是**让人来管理/审阅多 Agent 并行任务**——人看看板分配任务、review 代码变更。但 Nick 反映它「试过不 work」，原因是「人在多 Agent 并行时成为瓶颈」。

这与 SE 中的经典问题一致：并行任务的管理复杂度随规模指数增长，人肉协调成本高于收益。

### 3. Git 分支机制对比

**Multica 的分支机制（底层扎实但 UI 残）**：

```
bare clone 缓存（daemon 维护）
    └── 每个 issue: git worktree create <branch-name>  from  <baseRef>
```

- `baseRef` 来自 `runtime_config.DefaultBranchHint`（通过 `project resource add --default-branch-hint` 设置）
- 设置一次，整个项目继承，无需重复配置
- **UI 不暴露这个选项**，必须用 CLI

**VibeKanBan 的分支机制**：未详（推测是每个 issue 对应一个分支或 fork）

---

## 为什么 Multica 比 VibeKanBan「差」——从用户视角

Nick 的评价「M 比 VibeKanBan 差多了」是从**人类使用体验**出发的：

| 用户需求 | Multica | VibeKanBan |
|---------|---------|------------|
| 看代码 | ❌ 要 SSH | ✅ 看板式浏览 |
| 审变更 | ⚠️ 有 diff 文本，无 UI | ✅ 推测有 |
| 管理多 Agent | ✅ 任务面板 | ✅ 看板分配 |
| 指定分支 | ⚠️ 要用 CLI | 未详 |

VibeKanBan 如果有好的文件浏览 UI，在「让人舒服地管理 agent」这件事上确实领先 Multica。

---

## 结论与建议

**Multica 适合**：
- 需要**多 Agent CLI 统一调度**（尤其需要同时跑 Claude Code + Codex + Hermes）
- 接受 SSH / 容器内看代码
- 团队已有 agent，缺少统一的任务追踪层

**VibeKanBan 适合**：
- 以**人为主**的多 agent 看板管理
- 代码 review 需求优先于 agent 调度需求

**两者并不直接竞争**——VibeKanBan 解决「人看代码」的问题，Multica 解决「agent 协作」的问题。真正缺少的是一个同时把两件事做好的工具。

---

## Related

- [[Multica]] — Multica 完整 entity 页面
- [[se-and-llm-harness]] — Nick 的工具选型教训（包含 VibeKanBan 初版评估）
- [[raw/multica/README]] — Multica 源码文档
