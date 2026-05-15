---
title: superpowers-openspec
type: entity
created: 2026-04-28
updated: 2026-04-28
sources:
  - https://github.com/tielei60/superpowers-openspec
tags:
  - SDD
  - superpowers
  - openspec
  - skill
  - specification
  - AI-coding
---

# superpowers-openspec

**tielei60/superpowers-openspec** — 面向 OpenSpec 的方案、规范与计划工作流 skill。职责是把用户用中文表达的"方案、规范、计划、需求沟通"稳定路由到合适的 OpenSpec / OPSX 阶段，并补足方案先行、来源引用、图示建议和同步门禁。MIT License。

## 核心定位

不是重新定义 OpenSpec，而是帮助用户把方案、规范和计划的阶段边界说清楚，再将已确认内容落到官方 OpenSpec / OPSX 工作流。

**三层分工：**
- `superpowers` 负责调度
- `superpowers-openspec` 负责组织方案、规范与计划
- `OpenSpec / OPSX` 负责规范化产物

## 适用场景

满足任一情况时，触发本 skill：

1. **用户明确要求先做规范工作**：先分析 / 先写 spec / 先整理需求 / 先做详细设计 / 先写方案 / 先写计划
2. **规范意图和实现意图混在一起**："帮我设计并实现短信发送功能" / "先把方案定下来再开发"
3. **任务本身涉及规范阶段判断**：新功能 / 功能改造 / 功能优化 / 能力升级 / 业务规则变更 / 接口变更 / 数据模型变更 / 状态流转变化

## 路由决策规则

| 条件 | 建议 |
|------|------|
| 需求边界清晰 | `/opsx:propose` |
| 仍有未决项 | `/opsx:explore` 先探索 |
| 普通修复，影响范围明确 | 不进入，直接处理 |
| 局部性能优化，不涉及规则/流程/接口变化 | 不进入，直接处理 |
| 涉及规则、流程、接口、状态、数据结构变化 | 进入 `superpowers-openspec` |

## 方案文档先行门禁

- 如果用户要求先落地完整 markdown 方案，先生成 `docs/solutions/<主题>.md`
- **在用户确认方案文档之前，不创建或更新 OpenSpec change**
- 在请求用户确认前，先询问是否需要"方案文档自我闭环验证"

## OpenSpec 产物约束

- 不要把 `docs/solutions/*.md` 写成 OpenSpec 官方 artifact
- 不要新增 `sources.md` 或 `source-docs.md`
- `proposal.md`、`spec.md`、`design.md`、`tasks.md` 仍是官方工作区核心产物
- 如果 OpenSpec change 来源于方案文档，`proposal.md` 必须包含"来源方案文档"章节

## 图示规则

- 架构、流程、状态、时序等复杂关系优先用 **Mermaid**
- 页面、表单、列表、弹窗结构优先用 ASCII 文本布局图
- 输出 Mermaid 前要自检语法和结构

## 相关文件

- `SKILL.md` — skill 主说明，包含路由规则、门禁和停止条件
- `references/planning-workflow.md` — 完整方案文档先行流程
- `references/spec-template.md` — OpenSpec 产物承载建议
- `references/intent-to-openspec-mapping.md` — 中文意图 → OpenSpec 路由映射
- `evals/evals.json` — 评测用例集
- `scripts/qa.sh` — 仓库自检脚本
