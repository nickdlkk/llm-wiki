---
title: gstack /learn
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/learn/SKILL.md"]
tags: [gstack, memory, cross-session, learnings]
---

# /learn

Manage learnings.jsonl — 搜索、裁剪、导出、按类型统计 AI 在当前 repo 中学到的 pattern/pitfall/preference/architecture。跨 session 的持久化记忆，让 AI 不重复犯错。

## 核心职责

**learnings.jsonl** 是 gstack 的跨会话记忆文件，位于 `~/.gstack/projects/{slug}/learnings.jsonl`。每条记录包含：

- **类型**：`pattern`（有效做法）/ `pitfall`（踩过的坑）/ `preference`（用户偏好）/ `architecture`（架构决策）
- **内容**：具体描述
- **repo**：来自哪个项目
- **timestamp**：记录时间

## 使用场景

1. **新 session 开始**：skill 会自动读 `learnings.jsonl`，打印 relevant learnings
2. **遇到问题时**：查询是否有同类 pitfall 的历史记录
3. **做架构决策时**：查询是否有相关 architecture learnings
4. **理解用户偏好**：查询 preference 类型的 learnings

## 与其他 memory 系统的对比

| 系统 | 记忆范围 | 持久化 | 查询方式 |
|------|---------|--------|---------|
| **gstack learnings.jsonl** | 项目级（per repo） | JSONL 文件 | `gstack-learnings-search` CLI |
| **Hermes LLM Wiki** | 全局知识库 | Markdown 文件 | 语义搜索 |
| **OpenClaw Chorus** | Agent 任务历史 | Database | 会话查询 |
| **superpowers memory** | task plan + progress | Markdown | 文件追踪 |

gstack learnings 是**最轻量的项目级记忆**，聚焦于"在这个 repo 里 AI 踩过什么坑、发现过什么有效模式"。

## 与 GBrain 的关系

GBrain 是 gstack 的 semantic search 层：
- `gbrain search` — 语义搜索跨 repo 代码
- `gstack-learnings-search` — 只搜当前 repo 的 learnings
- 两者互补：learnings 记录"判断"，GBrain 记录"代码"

## 相关技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口（包含 Browse、Review 等核心 skill）
