---
title: Brainstorming
type: concept
created: 2026-05-28
updated: 2026-05-28
sources:
  - https://github.com/obra/superpowers
tags:
  - superpowers
  - skill
---

# Brainstorming

> Source: skills/brainstorming/SKILL.md

## 核心工作流

**9步流程：**

1. **Explore project context** — 检查文件、文档、最近提交，了解当前项目状态
2. **Offer visual companion** (if visual questions ahead) — 单独消息询问是否需要浏览器可视化伴侣
3. **Ask clarifying questions** — 一次一问，理解目的/约束/成功标准
4. **Propose 2-3 approaches** — 提出2-3种方案，含权衡分析和推荐
5. **Present design** — 按复杂度分段展示，每段获取用户确认
6. **Write design doc** — 保存至 `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`
7. **Spec self-review** — 检查占位符、一致性、范围、歧义
8. **User reviews written spec** — 用户审查书面规格后再继续
9. **Transition to implementation** — 调用 writing-plans skill 创建实施计划

**设计产出路径：** `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`

## 铁律 / HARD-GATE

**绝对禁令：** 在未展示设计方案并获得用户批准前，禁止：
- 调用任何实现类 skill
- 编写任何代码
- 搭建任何项目
- 采取任何实现行动

**唯一出口：** brainstorming 完成后只能调用 `writing-plans`，不能调用 frontend-design、mcp-builder 或其他实现技能。

## 与其他技能的关系

- **前置：** 用户提出需求/想法
- **后置：** 必须调用 `writing-plans` 创建实施计划
- **Visual Companion：** 浏览器可视化伴侣，单独消息发送，按需使用（mockups、layouts、diagrams）

## Red Flags

**Anti-Pattern 1: "这太简单不需要设计"**
- 每个项目都要走设计流程，包括 todo list、单个函数、配置变更
- "简单"项目正是未审查假设造成最多浪费的地方
- 设计可以很短，但必须展示并获得批准

**Anti-Pattern 2: 多问并发**
- 一次只问一个问题
- 多选题优先于开放式问题

**Anti-Pattern 3: 跳过验证直接实现**
- 未等待用户批准设计就进入实现
- 跳过用户审查书面规格的步骤

**Anti-Pattern 4: 无关重构**
- 在现有代码库中探索当前结构，遵循既有模式
- 不要提议无关的重构，专注于当前目标

---

**关键原则：** YAGNI 严格执行、增量验证、灵活调整
