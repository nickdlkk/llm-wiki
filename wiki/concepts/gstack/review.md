---
title: gstack /review
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/review/SKILL.md"]
tags: [gstack, code-review, pre-landing, safety-gates]
---

# /review

Pre-landing PR review — 分析 diff 相对于 base branch 的 SQL 安全、LLM trust boundary 违规、条件副作用、结构性问题。在代码合并 landing 前运行，是 CI 通过但生产挂掉的 bug 的最后一道防线。

## 核心职责

**何时触发**：
- 用户说 "review this PR"、"code review"、"check my diff"、"pre-landing review"
- 用户准备合并或 landing 代码变更时**主动建议**

**检查维度**（Staff Engineer 视角）：

| 维度 | 检查内容 |
|------|---------|
| **N+1 查询** | 数据库查询是否有多次循环调用？ |
| **竞态条件** | 并发场景是否有状态竞争？ |
| **信任边界** | 外部输入是否充分验证？ |
| **完整性缺口** | 错误处理是否覆盖所有分支？ |
| **SQL 安全** | 参数化查询是否正确？ |
| **条件副作用** | if/else 是否引入非预期状态变更？ |

## 与普通 code review 的区别

gstack `/review` 是**结构性**审查，不是泛泛看代码风格。它有明确的检查清单，聚焦于 AI 容易引入的错误类型：

- AI 倾向于写"看起来对"但运行时错的代码（特别是边界条件）
- AI 倾向于跳过错误处理（early return / null check 被省略）
- AI 倾向于用循环代替批量操作（N+1 问题）

## 铁律

**不修复，只报告**。review 发现问题后停下，等待人工确认是否修复。Review 不是执行 skill。

## 与 /plan-eng-review 的区别

| 维度 | /plan-eng-review | /review |
|------|-----------------|---------|
| 时机 | plan 阶段（写代码前） | pre-landing（代码完成后） |
| 重点 | 架构、数据流、边界情况 | 结构性 bug、N+1、安全 |
| 输出 | 锁定的技术方案 | 问题列表 + 修复建议 |

## 与 /plan-eng-review 的区别

/plan-eng-review 是 Eng Manager 视角，在 plan 阶段锁定架构和数据流；/review 是 pre-landing 检查，在代码完成后识别结构性 bug。

## 相关技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/qa]] — 真实浏览器 QA（review 通过后）
- [[concepts/gstack/ship]] — 合并+PR（review + qa 通过后）
- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
