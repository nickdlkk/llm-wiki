---
title: "AI DevOS PRD — 面向长任务自治的软件工程操作系统"
type: "summary"
created: "2026-05-15"
sources: ["raw/notes/ai-devos-prd.md"]
tags: ["AI-DevOS", "long-task-governance", "durable-execution", "AI-Agent", "feasibility-eval"]
---

# AI DevOS PRD — 面向长任务自治的软件工程操作系统

## 背景

当前 AI Agent 生态存在明显分层：Claude Code/Codex（强开发）、OpenHands/OpenClaw（强执行）、Hermes Agent（强 durable scheduling）、Multica/paperclip（强组织流程管理）。但**不存在统一系统**同时满足：长时间自治执行 + 软件工程连续性 + 开发流程治理 + 组织级任务协同 + 可恢复性 + 可验证性。

## 核心问题

1. **长任务不可持续**：Agent 只能持续工作数分钟，session 漂移、context 爆炸、无 recovery
2. **缺乏工程连续性**：通用 Agent 不理解 repo graph、architecture、dependency evolution
3. **缺乏长任务治理**：Kanban 系统只管 task，不管 runtime 生命周期和 context degeneration
4. **缺乏 Runtime 级调度**：没有 lease system、retry queue、dead-letter queue
5. **缺乏 Verification-driven**：Agent "生成代码 ≠ 验证代码"
6. **缺乏组织治理**：多 Agent 系统无 ownership、approval chain、budget system
7. **缺乏 Persistent Engineering Memory**：session 结束后架构决策丢失

## 核心理念

- Agent 是**长期存活的软件工程 worker**，不是聊天机器人
- 软件开发需要 architecture continuity、semantic memory、verification loops、governance
- 长任务治理 > Prompt Engineering — 关键是让 Agent **更稳定**而非更聪明
- Runtime Engineering > Agent Prompting — scheduling、recovery、verification、replay、memory

## 8层系统架构

Goal Layer → Planning Layer → Governance Layer → Scheduler Layer → Coding Runtime Layer → Workspace Layer → Verification Layer → Memory Layer

## 核心创新

- **Long-task Governance**：专门治理长时间软件工程任务
- **Verification-first**：execute → verify → recover → continue 闭环
- **Organizational Governance**：ownership + approval + audit + budgeting
- **Persistent Engineering Memory**：形成长期软件工程连续性

## 可行性评估结论

**可行（长远战略项目）**，核心技术组件有成熟实现。主要风险：4-8 小时 context 压缩稳定性 + verification 误判率。建议 Phase 0（1-2周）先验证核心假设，再全面投入。

详细评估：[[AI-DevOS]]（entity 页面）
