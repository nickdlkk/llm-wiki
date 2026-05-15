---
title: "finishing-a-development-branch"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/finishing-a-development-branch/SKILL.md
tags:
  - skills
  - git
  - merge
  - PR
  - workflow
  - superpower-with-files
---

# finishing-a-development-branch

> 实现完成，所有测试通过，需要决定如何集成工作时使用。引导完成开发工作，呈现结构化选项用于合并、PR 或清理。

## 流程

### Step 1: 验证测试

**呈现选项前必须验证测试通过：**

```bash
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：**
```
Tests failing (<N> failures). Must fix before completing:

[Show failures]

Cannot proceed with merge/PR until tests pass.
```
停止。不要到 Step 2。

**如果测试通过：** 继续 Step 2。

### Step 2: 确定 Base Branch

```bash
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

或问："这个分支从 main 分叉 — 对吗？"

### Step 3: 呈现选项

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**不要加解释** — 保持选项简洁。

### Step 4: 执行选择

#### Option 1: 本地合并

```bash
git checkout <base-branch>
git pull
git merge <feature-branch>
<test command>
git branch -d <feature-branch>
```
清理 worktree（Step 5）

#### Option 2: Push 并创建 PR

```bash
git push -u origin <feature-branch>
gh pr create --title "<title>" --body "..."
```
清理 worktree（Step 5）

#### Option 3: 保持 As-Is

报告位置。**不要清理 worktree。**

#### Option 4: Discard

**先确认：**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```
等精确确认。如果确认：
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```
清理 worktree（Step 5）

### Step 5: 清理 Worktree

**选项 1, 2, 4：**

```bash
git worktree list | grep $(git branch --show-current)
```
如果是：
```bash
git worktree remove <worktree-path>
```

**选项 3：** 保持 worktree。

## 快速参考

| 选项 | 合并 | Push | 保持 Worktree | 清理分支 |
|------|------|------|--------------|---------|
| 1. 合并本地 | ✓ | - | - | ✓ |
| 2. 创建 PR | - | ✓ | ✓ | - |
| 3. 保持 As-Is | - | - | ✓ | - |
| 4. 丢弃 | - | - | - | ✓ (force) |

## 常见错误

**跳过测试验证**
- 问题：合并坏代码，创建失败 PR
- 修复：验证测试后再提供选项

**开放式问题**
- 问题："我接下来做什么？" → 模糊
- 修复：呈现精确 4 选项

**自动 worktree 清理**
- 问题：选项 2, 3 时移除 worktree
- 修复：仅对选项 1 和 4 清理

**Discard 无确认**
- 问题：意外删除工作
- 修复：要求输入"discard"确认

## 在 superpower-with-files 中的角色

finishing-a-development-branch 是 **Planning → Execution → Integration 的最终节点**。被以下调用：
- `subagent-driven-development`（Step 7）— 所有任务完成后
- `spf-exec-plan`（Step 5）— 所有批次完成后

与 using-git-worktrees 配对：using-git-worktrees 创建 worktree，finishing-a-development-branch 清理 worktree。
