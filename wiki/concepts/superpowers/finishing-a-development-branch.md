---
title: Finishing a Development Branch
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Finishing a Development Branch

> Source: skills/finishing-a-development-branch/SKILL.md

## 核心工作流

### 5步流程

```
Step 1: Verify Tests
    ↓ (tests must pass)
Step 2: Determine Base Branch
    ↓
Step 3: Present Options (4 choices)
    ↓
Step 4: Execute Choice
    ↓
Step 5: Cleanup Worktree
```

**开始时宣布：** "I'm using the finishing-a-development-branch skill to complete this work."

## 4个选项

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

| 选项 | 合并 | 推送 | 保留 Worktree | 清理分支 |
|------|------|------|---------------|----------|
| 1. 本地合并 | ✓ | - | - | ✓ |
| 2. 创建 PR | - | ✓ | ✓ | - |
| 3. 保持现状 | - | - | ✓ | - |
| 4. 丢弃 | - | - | - | ✓ (force) |

## Option 4 强制确认机制

**丢弃前必须确认：**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

等待精确的确认输入。收到后才能执行删除。

## Worktree 清理规则

**清理（Options 1, 2, 4）：**
```bash
# 检查是否在 worktree 中
git worktree list | grep $(git branch --show-current)

# 如果是，移除
git worktree remove <worktree-path>
```

**保留（Option 3）：** 不清理 worktree

## 与其他技能的关系

**被以下调用：**
- **subagent-driven-development**（Step 7）：所有任务完成后
- **executing-plans**（Step 5）：所有批次完成后

**配套使用：**
- **using-git-worktrees**：清理该技能创建的 worktree

## Red Flags

**永远不要：**
- 在测试失败时继续
- 不验证合并结果就合并
- 未经确认删除工作
- 未经明确请求 force-push

**始终要：**
- 提供选项前验证测试
- 呈现恰好 4 个选项
- Option 4 需要输入 "discard" 确认
- 仅对 Options 1 和 4 清理 worktree

## 常见错误

**跳过测试验证**
- 问题：合并坏代码，创建失败的 PR
- 修复：提供选项前始终验证测试

**开放式问题**
- 问题："接下来做什么？" → 模糊
- 修复：呈现恰好 4 个结构化选项

**自动清理 worktree**
- 问题：可能还需要时移除 worktree（Option 2, 3）
- 修复：仅对 Options 1 和 4 清理

**丢弃时无确认**
- 问题：意外删除工作
- 修复：要求输入 "discard" 确认
