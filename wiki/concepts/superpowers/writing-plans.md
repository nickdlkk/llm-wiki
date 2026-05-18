---
title: Writing Plans
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Writing Plans

> Source: skills/writing-plans/SKILL.md

## 核心工作流

### 1. 范围检查 (Scope Check)
- 如果 spec 覆盖多个独立子系统，建议拆分为多个计划
- 每个计划应产生可独立测试的软件

### 2. 文件结构映射 (File Structure)
- 先映射将创建或修改的文件，明确每个文件的职责
- 设计边界清晰、接口定义明确的单元
- 遵循现有代码库的既有模式
- 文件按职责拆分，而非按技术层拆分

### 3. 任务粒度
**每个步骤 2-5 分钟：**
- "Write the failing test" - 步骤
- "Run it to make sure it fails" - 步骤
- "Implement the minimal code to make the test pass" - 步骤
- "Run the tests and make sure they pass" - 步骤
- "Commit" - 步骤

### 4. 计划文档结构

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: 
> superpowers:subagent-driven-development (recommended) 
> 或 superpowers:executing-plans

**Goal:** [一句话描述构建目标]
**Architecture:** [2-3句方法论]
**Tech Stack:** [关键技术和库]

---

### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**
- [ ] **Step 2: Run test to verify it fails**
- [ ] **Step 3: Write minimal implementation**
- [ ] **Step 4: Run test to verify it passes**
- [ ] **Step 5: Commit**
```

## 铁律 / HARD-GATE

**No Placeholders 规则 — 禁止出现：**
- "TBD"、"TODO"、"implement later"、"fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above"（无实际测试代码）
- "Similar to Task N"（重复代码）
- 描述做什么但未展示如何做的步骤
- 引用未在任何任务中定义的类型/函数/方法

**原则强调：** DRY · YAGNI · TDD · frequent commits

## 与其他技能的关系

**两种执行选项：**

| 选项 | 推荐度 | 说明 |
|------|--------|------|
| **Subagent-Driven** | ⭐ 推荐 | 每个任务分发新的 subagent，任务间审查，快速迭代 |
| **Inline Execution** | 备选 | 在当前会话中执行，使用 executing-plans，带检查点批量执行 |

**前置：** brainstorming skill 创建的 spec
**后置：** subagent-driven-development 或 executing-plans

## Red Flags

**Plan 失败模式：**
1. **占位符扫描** — 搜索 "TBD"、"TODO"、不完整部分
2. **类型不一致** — 后期任务中的方法名/属性名与早期定义不匹配
3. **规格覆盖缺失** — spec 中的需求没有对应任务实现

**修复方式：** 内联修复，无需重新审查 — 发现即修复。
