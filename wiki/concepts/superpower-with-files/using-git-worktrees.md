---
title: "using-git-worktrees"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/using-git-worktrees/SKILL.md
tags:
  - skills
  - git
  - worktree
  - isolation
  - engineering-primitives
  - superpower-with-files
---

# using-git-worktrees

> 开始需要与当前工作区隔离的功能工作或执行实现计划前使用。创建共享同一仓库的隔离 git worktree，允许同时在多分支工作而不切换。

**核心原则**：系统化目录选择 + 安全验证 = 可靠隔离。

## 目录选择优先级

### 1. 检查现有目录

```bash
# 优先检查
ls -d .worktrees 2>/dev/null     # 首选（隐藏）
ls -d worktrees 2>/dev/null      # 备选
```

**如果存在：** 使用它。如果两者都存在，`.worktrees` 优先。

### 2. 检查 CLAUDE.md

```bash
grep -i "worktree.*director" CLAUDE.md 2>/dev/null
```

**如果指定了偏好：** 不问直接使用。

### 3. 问用户

如果无目录存在且无 CLAUDE.md 偏好：
```
No worktree directory found. Where should I create worktrees?

1. .worktrees/ (project-local, hidden)
2. ~/.config/superpowers/worktrees/<project-name>/ (global location)

Which would you prefer?
```

## 安全验证

### 项目本地目录（.worktrees 或 worktrees）

**创建 worktree 前必须验证目录被忽略：**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**如果未忽略：**

按 Jesse 规则"立即修复坏东西"：
1. 加对应行到 .gitignore
2. 提交变更
3. 继续创建 worktree

**关键原因：** 防止 worktree 内容意外提交到仓库。

### 全局目录（~/.config/superpowers/worktrees）

无需 .gitignore 验证 — 完全在项目外。

## 创建步骤

### 1. 检测项目名

```bash
project=$(basename "$(git rev-parse --show-toplevel)")
```

### 2. 创建 Worktree

```bash
case $LOCATION in
  .worktrees|worktrees)
    path="$LOCATION/$BRANCH_NAME"
    ;;
  ~/.config/superpowers/worktrees/*)
    path="~/.config/superpowers/worktrees/$project/$BRANCH_NAME"
    ;;
esac

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

### 3. 运行项目 Setup

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

### 4. 验证干净 Baseline

```bash
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：** 报告失败，问是否继续或调查。

**如果测试通过：** 报告就绪。

### 5. 报告位置

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## 快速参考

| 情况 | 行动 |
|------|------|
| `.worktrees/` 存在 | 使用它（验证忽略） |
| `worktrees/` 存在 | 使用它（验证忽略） |
| 两者都存在 | 使用 `.worktrees/` |
| 都不存在 | 检查 CLAUDE.md → 问用户 |
| 目录未忽略 | 加到 .gitignore + 提交 |
| 测试 baseline 失败 | 报告失败 + 问 |

## 常见错误

**跳过忽略验证**
- 问题：Worktree 内容被 track，污染 git status
- 修复：创建前始终用 `git check-ignore`

**假设目录位置**
- 问题：创建不一致，违反项目约定
- 修复：遵循优先级：现有 > CLAUDE.md > 问

**用失败测试继续**
- 问题：无法区分新 bug 和已有问题
- 修复：报告失败，明确许可才继续

## 在 superpower-with-files 中的角色

using-git-worktrees 是**隔离工程原语**，与 finishing-a-development-branch 配对使用：
- using-git-worktrees：**创建**隔离 worktree
- finishing-a-development-branch：**清理** worktree

它是 superpower-with-files 框架"分解"思想的体现——多分支并行工作时用 worktree 隔离，避免切换分支的风险和低效。
