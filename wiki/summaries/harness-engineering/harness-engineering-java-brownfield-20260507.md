---
title: "Harness Engineering 实战：存量 Java 工程的 AI Coding 率从 25% 提升至 90%"
type: summary
created: "2026-05-11"
updated: "2026-05-11"
sources:
  - "https://news.qq.com/rain/a/20260507A020NO00"
tags:
  - Harness-Engineering
  - AI-Coding
  - Brownfield
  - Enterprise-Java
  - Agent-Engineering
---

# Harness Engineering 实战：存量 Java 工程的 AI Coding 率从 25% 提升至 90%

> **一句话总结：** 用外部化的约束体系（Rules + Skills + Wiki + Changes）替代对模型内在能力的依赖，让 AI Coding 在企业级存量 Java 工程中从"不可控"变为"可预期"。AI 代码率从 24.86% → 90.54%，同时质量门禁全程在线。

## 背景

- **场景：** 10万+行 Java 1.8/Spring Boot/LiteFlow/HSF/Diamond/Tair 企业级应用
- **问题：** Agent 写出的代码语法正确但业务语义存在微妙错误（隐性知识未文档化）
- **根因：** Anthropic 指出的根本性限制 —— *"Agents are incapable of accurately evaluating their own work"*

## 核心架构：.harness/ 四要素

```
.harness/
├── agents/          # Application Owner Agent（~400行，承担 Index & Map 职责）
├── rules/           # 稳定约束：工程结构 / 开发流程 / 编码规范
├── skills/          # 9个标准化 Skill（SOP）
├── changes/         # 变更追溯：每个需求的全链路文档
└── mcp/             # 外部工具集成
```

**四要素职责：**
- **Rules** → 告诉 Agent "标准是什么"（Invariant Constraints）
- **Skills** → 告诉 Agent "应该怎么做"（Reusable Workflows）
- **Wiki** → 告诉 Agent "系统是什么样的"（Domain Context）
- **Changes** → 记录 Agent "做了什么"（Audit Trail）

## Anthropic 4 种失败模式（Failure Modes）

| 失败模式 | 描述 | 对策 |
|---------|------|------|
| One-shot Syndrome | 上下文超过 40% 填充率后质量衰退 | 分层加载上下文，避免单次上下文过载 |
| Premature Victory Declaration | 编译都不通过就宣布完成 | 结构化执行流程 + 质量门禁 |
| Premature Feature Completion | 未做端到端测试就交付 | 引入 Puppeteer MCP 端到端验证 |
| Cold Start Problem | 每次新会话重头理解项目 | 进度持久化到文件系统 |

## 四根支柱

1. **上下文架构** — AGENTS.md 控制在 ~100 行作为 Index & Map，上下文分层 L1/L2/L3 按需加载
2. **Agent 专业化** — Planner/Generator/Evaluator 三角色分离；"做事 Agent"和"评判 Agent"分开是 Powerful Lever
3. **持久化记忆** — 进度写文件系统，启动时读 progress.md 定位最高优先级任务
4. **结构化执行** — 理解→规划→执行→验证，每阶段有 Entry Criteria / Skill Injection / Quality Gate

## 十阶段流程（10-Stage Pipeline）

```
需求分析 → 需求评审 → 编码实现 → 编码评审 → 单元测试编写
  → 单元测试评审 → 代码推送 → CI 验证 → 部署验证 → 用户确认
```

关键机制：
- **回退路径：** CI 失败（0/0 测试）→ 回退到阶段5；编译错误 → 回退到阶段3
- **Iteration Cap：** 需求评审≤3轮，编码/测试评审≤2轮，超出升人工
- **5 个 Human-in-the-Loop 确认点**

## Skill 体系示例

**coding-skill 硬性约束（分层编码 Spec）：**
- 价格字段 → `long` 类型（单位为分），禁止 `double`/`float`
- 外部 RPC → 必须设置超时和降级
- 流程编排组件 → 委托 Service 处理，组件内不写大段业务逻辑

**expert-reviewer：** Plan Review（spec.md + tasks.md） + Execution Review（编码实现），每条意见含 MUST FIX / LOW / INFO 优先级

**unit-test-write：** Change-driven Testing —— 改了哪个接口就测哪个接口，而非测最上层

## 关键工程经验

### 门禁必须可程序化验证

> "If it can't be mechanically enforced, the agent will drift." — OpenAI 百万行代码经验

❌ `status == SUCCESS` → Agent 忽略测试用例数为 0
✅ `status == SUCCESS && total_tests > 0 && passed == total`

### Entropy Garbage Collection

Agent 模仿次优 Pattern 累积导致 Code Rot。解决：Golden Principles 编码化，后台 Agent 自动扫描违规并提修复 PR。

### 分离执行与评判是关键杠杆

评审 Agent 发现了编码 Agent 遗漏的渠道判断逻辑（潜在线上故障），以及 Agent 试图跳过评审阶段的行为。

## 效果数据

| 维度 | 无 Harness | 有 Harness |
|------|-----------|-----------|
| 项目 AI 代码率 | 24.86% | **90.54%** |
| 返工轮次 | 3-5 轮 | **1 轮** |
| 测试覆盖 | 形式化测试 | 18 个有业务价值的真实测试用例 |
| 过程可追溯性 | 无记录 | 完整 Audit Trail |

## 相关资料

- [[Harness Engineering]] — 五大子系统概念页
- [[Four-Layer Feedback Loop]] — 编译→单测→e2e→CI 四层反馈闭环
- [[Three Engineering Primitives]] — 隔离 / 分解 / 协调
- [[Agent Review Pattern]] — Agent 审 Agent 的机制
- [Anthropic: Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [OpenAI: Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)
