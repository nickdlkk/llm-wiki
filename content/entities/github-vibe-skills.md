---
title: Vibe-Skills
type: entity
source: github:foryourhealth111-pixel/Vibe-Skills
created: 2026-04-17
updated: 2026-04-17
tags:
  - vibe-skills
  - skill-system
  - governed-runtime
  - multi-agent
  - memory-system
  - claude-code
  - codex
  - windsurf
  - python
---

# Vibe-Skills

*"More than a skill collection — your personal AI operating system."*

**Repo:** `foryourhealth111-pixel/Vibe-Skills` | **Lang:** Python + Markdown | **License:** MIT | **Status:** Active | **Skills:** 340+

---

## Summary

Vibe-Skills 是一个**大规模技能生态系统 + 受治理的运行时框架**。VCO（Vibe Code Orchestrator）是其核心运行时，通过 `/vibe` 入口协调整个任务生命周期。

**核心解决的问题：**
- 340+ 技能不会同时竞争 — 智能路由决定哪个技能在哪个阶段激活
- AI 不能盲目执行 — 必须 Clarify → Plan → Execute → Verify
- 多插件/多工作流不打架 — 129 条治理规则定义安全边界和降级机制
- 工作区长期使用不乱 — 固定架构语义目录治理

---

## Architecture

```
Vibe-Skills/
├── SKILL.md              # VCO 受治理运行时入口
├── core/                 # VCO 核心
├── bundled/skills/       # 342 个技能包
├── commands/             # vibe / vibe-want / vibe-how / vibe-do 入口
├── protocols/            # 6 个协议：runtime / think / do / review / team / retro
├── rules/                # 129 条治理规则
├── references/           # 契约、规范、评分卡
├── agents/templates/     # Agent 模板
├── mcp/profiles/         # MCP 配置
├── adapters/             # 多 Host 适配器（Claude Code / Codex / Windsurf / OpenClaw / OpenCode / Cursor）
├── apps/vgo-cli/         # VCO Python CLI 引擎
└── docs/                 # 大量治理文档（governance）
```

---

## VCO Governed Runtime

`/vibe` 是唯一权威运行时入口。

### 6-Stage State Machine

```
skeleton_check → deep_interview → requirement_doc → xl_plan → plan_execute → phase_cleanup
```

- `skeleton_check`: 检查 repo 形状、活跃分支、现有计划
- `deep_interview`: 生成结构化意图契约（goal/deliverable/constraints/acceptance criteria...）
- `requirement_doc`: 冻结需求文档到 `docs/requirements/`
- `xl_plan`: 生成执行计划到 `docs/plans/`
- `plan_execute`: 按 L/XL grade 执行
- `phase_cleanup`: 清理 + 证明 artifacts

### 4 Entry Points

| 入口 | 行为 |
|------|------|
| `vibe` | 完整受治理流程（到 phase_cleanup） |
| `vibe-want` | 只到 requirement_doc（冻结需求后停止） |
| `vibe-how` | 只到 xl_plan（冻结需求+计划后停止） |
| `vibe-do-it` | 完整流程，不跳过需求或计划 |

---

## M / L / XL Execution Grades

内部执行策略，由运行时自动选择：

| Grade | Use Case | Behavior |
|-------|----------|----------|
| **M** | 窄范围、边界清晰的工作 | 单 Agent，token 高效，快速响应 |
| **L** | 中等复杂度，需要设计、计划、评审 | 受治理多步骤串行执行 |
| **XL** | 大型任务，可并行的独立部分 | Coordinator 拆分为 bounded units，波次串行 + 有限并行 |

**Root/Child Lane 语义（XL 专用）：**
- `root_governed`: 唯一可以冻结规范需求和计划、发布最终完成声明的 lane
- `child_governed`: 继承冻结上下文，发出本地 receipt，不发布最终声明

---

## Intelligent Routing

340+ 技能不会同时激活。`vibe` 是唯一协调器，专科技能只在特定阶段或 bounded work unit 需要时才引入。

- **单一主路由**: `vibe` 负责整体受治理流程
- **专科技能按需引入**: requirement / planning / execution / verification 各阶段可拉取不同技能，但只服务于该阶段
- **治理规则 + 优先级排序 + 互斥规则** 防止同层技能冲突

---

## Memory System (4 Layers)

| Layer | Owner | Scope | Function |
|-------|-------|-------|---------|
| **Session memory** | `state_store` | 当前会话 | 执行进度、临时状态、中间结果 |
| **Project memory** | `Serena` | 当前 workspace / 项目 | 确认的架构决策、项目规范、长期工作协议 |
| **Task-semantic memory** | `ruflo` | 会话内 / 长任务检索 | 大型任务中易检索的相关上下文片段 |
| **Long-term knowledge memory** | `Cognee` | 跨会话知识 | 实体、关系、持久知识链接 |

> `mem0` 可作为个人偏好后端，`Letta` 可提供 memory-block 映射词汇表。两者都不替代上面的规范角色。

---

## Key Differentiators

1. **Governed runtime, not just skill collection** — `/vibe` 强制执行 Clarify → Plan → Execute → Verify，而不是让 AI 直接开始
2. **Intelligent routing** — 340+ 技能不会互相干扰，路由器决定哪个技能在什么时候激活
3. **129 governance rules** — 全局治理规则定义安全边界和降级机制
4. **M/L/XL grades** — 任务复杂度自动选择执行策略，XL 支持有界的并行多 Agent
5. **Root/Child lane isolation** — XL 多 Agent 中，root 冻结规范，child 在 bounded scope 内执行
6. **4-layer memory** — 分层记忆防止上下文污染
7. **Multi-host compatibility** — Claude Code / Codex / Windsurf / OpenClaw / OpenCode / Cursor + MCP

---

## vs Other Tools

| Aspect | Vibe-Skills | OpenSpec | GenericAgent |
|--------|-------------|----------|-------------|
| Core concept | Governed runtime + 340+ skills | Spec delta format | Self-evolving skills |
| Entry point | `/vibe` (6-stage state machine) | `/opsx:propose` | Task → explore → crystallize |
| Skill count | 340+ | 26 adapters | ~3 base + auto-crystallized |
| Governance | 129 contract rules | Delta tracking | Memory layers |
| Execution | M/L/XL grades, Root/Child lanes | Artifact-driven | 9 atomic tools |
| Memory | 4-layer (session/project/task/knowledge) | Profile-based | L0-L4 layered |
| Multi-host | 6+ AI environments | 26 adapters | Streamlit/Qt/微信等 |

---

## Key Files

- `SKILL.md` — VCO 受治理运行时完整规范（386行）
- `bundled/skills/` — 342 个技能包（每个是一个完整技能）
- `protocols/runtime.md` — 受治理运行时契约
- `protocols/think.md` — 规划、研究、预执行分析协议
- `protocols/do.md` — 编码、调试、验证协议
- `protocols/review.md` — 评审和质量门协议
- `protocols/team.md` — XL 多 Agent 编排协议
- `protocols/retro.md` — 回顾和学习捕获协议
- `rules/` — 129 条治理规则

---

## Strengths

- 规模最大、最完整的技能生态系统之一（340+）
- 受治理运行时防止 AI 盲目执行和静默失败
- 多 Host 兼容（Claude Code / Codex / Windsurf / OpenClaw / OpenCode / Cursor + MCP）
- 完善的治理框架，129 条规则确保长期稳定性
- 多 Agent 编排支持（Root/Child lane 隔离）
- 活跃维护，持续演进

## Weaknesses

- 学习曲线陡峭 — 340+ 技能 + 129 规则需要大量熟悉成本
- 框架复杂，对于简单任务 overhead 过高
- VCO CLI 依赖 Python，Windows PowerShell 支持有限
- 大量治理文档可能导致信息过载
