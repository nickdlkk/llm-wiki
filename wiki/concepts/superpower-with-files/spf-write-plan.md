---
title: "spf-write-plan"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/spf-write-plan/SKILL.md
tags:
  - skills
  - planning
  - TDD
  - implementation-plan
  - superpower-with-files
---

# spf-write-plan

> 有规格或需求的多步任务时，在接触代码前使用。产出：咬碎的可执行实现计划，每步 2-5 分钟。

## 概述

写全面实现计划，假设工程师对代码库零上下文、味觉可疑。记录他们需要知道的一切：每个任务改哪些文件、代码、测试、可能需要检查的文档。怎么测试它。给他们完整计划作为咬碎任务。DRY。YAGNI。TDD。频繁提交。

## 计划文档头部

**每个计划必须以此头部开始：**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [一句话描述构建什么]

**Architecture:** [2-3 句方法]

**Tech Stack:** [关键技术和库]

---
```

## 任务结构

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**
[完整测试代码]

**Step 2: Run test to verify it fails**
Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**
[最小实现]

**Step 4: Run test to verify it passes**
Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**
```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## 自适应粒度

**在计划头部加复杂度标记：**

- `simple`：将模板和逻辑合并为更少更大任务
- `medium/complex`：使用**拆分架构**（详参下）

### 拆分架构（verbose 减少）

中/复杂项目，`active_tdd_plan.md` 必须保持简洁的高层任务列表。详细实现指令必须移到：

**`.superpower-with-files/guides/task-N.md`**

在主计划中引用指南：`Guide: See guides/task-N.md`

## 依赖 & 并行标记

每任务块必须包含：
- `Depends on: [Task ID | None]`
- `Parallel with: [Task ID | None]`

## 智能测试检测

起草测试部分前，**必须**自动检测测试运行器：
- `Cargo.toml` → `cargo test`
- `pyproject.toml` / `requirements.txt` 含 pytest → `pytest`
- `package.json` → `npm test`
- 无检测 → 问用户

## 模板支持

如果提到特定框架或语言（如 "Python CLI", "React Component"），**必须**检查 `templates/` 目录并用对应模板指导结构和库选择。

## 执行交接

计划保存后，提供执行选项：

**"计划已保存到 `.superpower-with-files/active_tdd_plan.md`。两个执行选项：**

**1. Subagent-Driven（本 session）** - 每任务调度 fresh subagent，任务间审查，快迭代

**2. Parallel Session（新 session）** - 在 worktree 中开新 session 用 executing-plans 批量执行

**选哪个？"**

## 严格仅计划

- 不执行代码或运行测试
- 完成后提示："Planning phase complete. 审查后使用 **'Execute the plan.'** 继续。"

## 在 superpower-with-files 中的角色

spf-write-plan 是 Planning Phase 的**核心产出**。它接收 brainstorming 的输出（已批准的设计），产出 `.superpower-with-files/active_tdd_plan.md`，作为 Execution Phase 的输入。它与 brainstorming 一起完成完整的 Planning Loop。
