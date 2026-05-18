---
title: using-git-worktrees
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
  - engineering-primitives
---

# using-git-worktrees

> Source: `skills/using-git-worktrees/SKILL.md`

Git worktrees 创建隔离工作区（共享同一仓库），允许多分支同时工作无需切换分支。

## 核心流程

```
目录选择 → 安全验证 → 创建 worktree → 运行项目初始化 → 验证干净基线 → 报告位置
```

### 目录选择（优先级）

1. 检查 `.worktrees/` 或 `worktrees/` 是否存在
2. 检查 `CLAUDE.md` 中的 worktree 偏好
3. 如无 → 询问用户

### 安全验证（项目内目录必须）

```bash
git check-ignore -q .worktrees  # 确认目录在 .gitignore 中
# 若不在 → 添加到 .gitignore → commit → 再创建 worktree
```

**原因：** 防止 worktree 内容意外提交到仓库。

### 项目初始化（自动检测）

```bash
# Node.js
npm install          # 存在 package.json
# Rust
cargo build          # 存在 Cargo.toml
# Python
pip install -r requirements.txt  # 存在 requirements.txt
poetry install       # 存在 pyproject.toml
# Go
go mod download      # 存在 go.mod
```

### 验证干净基线

运行测试套件 → 必须全部通过才能开始实现。

## 产出格式

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## 铁律

- 项目内目录创建前必须 `git check-ignore` 验证
- 必须验证干净测试基线
- 不得跳过 CLAUDE.md 检查

## 集成

| 调用方 | 时机 |
|--------|------|
| `brainstorming` | 设计批准后（Phase 4） |
| `subagent-driven-development` | 执行任务前（REQUIRED） |
| `executing-plans` | 执行任务前（REQUIRED） |

- 配对：`finishing-a-development-branch` 负责清理
