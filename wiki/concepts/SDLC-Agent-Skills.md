---
title: "SDLC Agent Skills Pattern"
type: concept
created: 2026-05-07
updated: 2026-05-07
sources: ["raw/articles/agent-skills.md", "summaries/agent-skills.md"]
tags: [agent, SDLC, engineering-workflow, quality-gate, anti-rationalization]
---

# SDLC Agent Skills Pattern

把软件开发生命周期（SDLC）的每个阶段封装成 AI Agent 可执行的结构化 Skill，是当前 agent-skills 生态的主流范式。

## 核心思想

传统 SDLC：Define → Plan → Build → Verify → Review → Ship
Agent 适配：每个阶段对应一个/多个可执行的 SKILL.md，含触发条件、步骤、验证门禁、反理智化表格。

## 六阶段详解

### 1. Define（定义）
**目标**: 把模糊想法变成具体提案

- `idea-refine`: 发散→收敛思维，把粗糙概念落地
- `spec-driven-development`: 写 PRD（目标、命令、结构、代码风格、测试、边界）

**触发**: `/spec` 或 agent 感知到"需求模糊"

### 2. Plan（计划）
**目标**: 把 spec 分解成可验证的小任务

- `planning-and-task-breakdown`: 原子任务 + 验收标准 + 依赖排序

**触发**: `/plan` 或 spec 完成后自动激活

### 3. Build（构建）
**目标**: 薄片式增量实现

| Skill | 职责 |
|-------|------|
| `incremental-implementation` | 垂直切片，feature flag，安全回滚 |
| `test-driven-development` | 红绿重构，测试金字塔（80/15/5）|
| `context-engineering` | 规则文件、上下文打包、MCP 集成 |
| `source-driven-development` | 官方文档验证，source citation |
| `frontend-ui-engineering` | 组件架构、设计系统、无障碍 |
| `api-and-interface-design` | 契约优先，Hyrum's Law，One-Version Rule |

**触发**: `/build` 或 agent 识别到代码实现任务

### 4. Verify（验证）
**目标**: 证明功能正确

- `browser-testing-with-devtools`: Chrome DevTools MCP 实时数据
- `debugging-and-error-recovery`: 五步 triage（复现→定位→简化→修复→防护）

**触发**: `/test` 或测试失败/构建中断

### 5. Review（审查）
**目标**: 合入前的质量门禁

| Skill | 重点 |
|-------|------|
| `code-review-and-quality` | 五轴审查（正确性/可读性/架构/安全/性能），~100行变更 |
| `code-simplification` | Chesterton's Fence，Rule of 500 |
| `security-and-hardening` | OWASP Top 10，三层边界系统 |
| `performance-optimization` | 测量优先，Core Web Vitals |

**触发**: `/review` 或 `/code-simplify`

### 6. Ship（交付）
**目标**: 安全部署

| Skill | 职责 |
|-------|------|
| `git-workflow-and-versioning` | trunk-based dev，原子提交 |
| `ci-cd-and-automation` | Shift Left，quality gate pipelines |
| `deprecation-and-migration` | code-as-liability，zombie code 清理 |
| `documentation-and-adrs` | 记录决策的"为什么" |
| `shipping-and-launch` | 上线 checklist，feature flag 生命周期 |

**触发**: `/ship`

## Anti-Rationalization 机制

每个 skill 内置 **Rationalizations 表**，列出 agent 常用来跳过步骤的借口及反驳。

例如 TDD skill：
| Excuse | Rebuttal |
|--------|----------|
| "这段代码太简单不需要测试" | 简单代码最容易被改坏 |
| "后面再加测试" | 后面不会加 |
| "测试降低开发速度" | 有测试的代码长期维护速度更快 |

## 关键设计原则

```mermaid
graph LR
    A[模糊想法] --> B[Define<br/>spec/PRD]
    B --> C[Plan<br/>原子任务]
    C --> D[Build<br/>薄片实现]
    D --> E[Verify<br/>测试+调试]
    E --> F[Review<br/>五轴审查]
    F --> G[Ship<br/>安全上线]
    G --> H[新需求]
    H --> A
```

1. **Process not prose** — 是工作流，不是参考文档
2. **Verification non-negotiable** — 必须有可验证证据
3. **Progressive disclosure** — 按需加载，不浪费 token
4. **Platform-agnostic** — 同一个 skill 可跨 Claude Code/Gemini CLI/OpenCode 使用

## 与 Hermes Skill 的关系

Hermes Skill 是任务导向的工具链（browser-automation、llm-wiki 等），agent-skills SDLC Pattern 是流程导向的工程规范。两者互补：agent-skills 定义"做什么步骤"，Hermes Skill 定义"用什么工具做"。

## 典型实现

- [agent-skills](https://github.com/addyosmani/agent-skills)（Addy Osmani）— 本页面主题
- Hermes 内置 skills 系统 — `/plan`, `/spec` 等命令
