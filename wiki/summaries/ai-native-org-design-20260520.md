---
title: "AI Native 时代 —— 研发组织何去何从"
type: summary
created: 2026-05-20
updated: 2026-05-20
sources:
  - https://mp.weixin.qq.com/s/Xf3C60jCxR4ppMi4HuAnVA
tags:
  - ai-native
  - organization-design
  - execution-graph
  - harness-engineering
  - management
  - platform-architecture
  - death-of-ego
author: 许晓斌
summary: AI Native 转型不是又一次 reorg，而是组织运作逻辑的根本重设计。核心转变：从 Org Chart 到 Execution Graph（节点 = 人/Agent/数据/权限/工具/审批），重组成本从季度级压到 week 级。新瓶颈不是 AI 能力不够，而是系统信息形态不够（人肉中间件）。Platform 三柱架构：Agent Platform Group / Domain Teams / Risk and Oversight。Architect 是新组织最高杠杆点。Death of ego 有边界：执行类工作死防御性 ego，创新类工作保护生产性 ego。
---

# AI Native 时代 —— 研发组织何去何从

**一句话总结：** AI Native 转型不是"让 AI 干活"的效率故事，而是把组织最小单元从"人+关系网"换成"任务+上下文+权限+工具"——这才是适应性速度的数量级跃迁。

## 核心洞察

### 从 Org Chart 到 Execution Graph

| | 旧范式 | 新范式 |
|--|--------|--------|
| 最小单元 | 人 + 长期关系网 | 任务 + 上下文 + 权限 + 工具 |
| 核心问题 | ownership（谁拥有这件事） | routing + governance（意图怎么进入系统并安全执行） |
| 重组成本 | 季度级（6-12个月） | week 级 |
| 信息形态 | 人脑隐性关系 | 机器可读 artifact |

**关键引用（Ken Huang）：** "Once AI becomes agentic, the organization stops being accurately described by an org chart. It becomes an execution graph."

### AI 不是工具，是新协作主体

AI 和人形成**镜像反面**：

| 人 | AI |
|----|----|
| 沟通衰减 | 无衰减 |
| 需要激励 | 不需要 |
| 有情绪和疲劳 | 无 |
| context switching 成本高 | 极小 |
| 记忆和注意力有限 | 几乎无限 |

### 双层结构：Harness 层 + Hive Mind 层

| | 特征 | 主导 |
|--|------|------|
| **底层 Harness 层** | 极度结构化：代码/测试/流水线/文档/世界模型，越结构化越好 | AI |
| **上层 Hive Mind 层** | 极度松散：对话/试错/idea涌现/Yes-and，越松散越好 | 人 |

> Anthropic 有比任何公司都精密的 Harness，但在上面运行混乱文化。这两件事不是替代，是叠加。结构化是为了释放无结构的协作，不是用结构控制一切。

### 新瓶颈：信息形态的人形偏置

当前 AI 化的真实瓶颈：**不是 AI 能力不够，是系统信息形态不够**。

- 员工已经在用 AI，但卡在"人肉中间件"阶段
- AI 能处理，但系统没给 AI 留接口（系统是为人设计的）
- 过去几十年被人悄悄补上的"隐性信息缺口"：不完整的需求、没注释的代码、不一致的 API 约定、口头潜规则

**解决方案：Harness Engineering** — 把组织的隐性 know-how 翻译成 AI 可消化形态。

## 核心架构

### Platform 三柱架构（Ken Huang）

| 柱子 | 定位 | 说明 |
|------|------|------|
| **柱1 · Agent Platform Group** | 建造底层 | runtime 标准、权限、日志、可观测、评估 harness、安全部署 |
| **柱2 · Domain Teams** | 上层探索交付 | 3-5 人垂直功能小组，Own outcomes rather than models |
| **柱3 · Risk and Oversight** | 守护底层 | "An immune system rather than a bureaucratic brake" |

> 柱3 最被低估。AI Native 讨论都在讲创造性/效率/文化，没人正面回答"出事了怎么办"。

### 管理塌缩的精确拆解

把管理者工作（10件事）按 AI 可替代性分类：

| 可被系统替代 | 形态在变 | 不可替代 | 新出现 |
|-------------|---------|---------|--------|
| 战略传导 | 重大决策（→ DRI） | 激励 | 意图教练 |
| 信息聚合 | 冲突调解（总量减少） | 辅导 | 身份重建 |
| 资源协调 | | 招聘退出 | 虚无对抗 |
| 日常决策 | | 文化建设 | |

### Architect：新组织的最高杠杆点

**定义：** 把组织的隐性 know-how 翻译成 AI 可消化形态的人。

**职责：** 为整个 Execution Graph 设计架构 — 定义系统能力边界、设计 SOP、建立测试基础设施、搭建集成与 triage 系统、定义"什么叫好"。

**为什么是资深工程师：** 写好的测试需要懂业务；写好的 SOP 需要做过这件事的人；定义好的判据需要有品味的人；设计好的架构需要见过失败模式的人。

**杠杆效应：** 一个 Architect 的产出会被 N 个 agent 复用、被多个 domain team 依赖、被多个项目继承。

### Agent：被忽略的新员工类型

**治理不对称（4个）：**
- 可被无限复制
- 同小时既 brilliant 又 brittle
- compliance-blind by default
- fast enough to fail at scale

> 管 agent 不是管软件、也不是管人，需要第三套治理框架。

## Death of Ego 的边界

**防御性 ego**（保护地盘、隐藏失败、邀功避责）→ Yegge 想杀的是这种，执行类工作，死 ego 是对的。

**生产性 ego**（"这是我要弄明白的"、"我不接受这个答案"、"这件事没想透我睡不着"）→ 创新的发动机。

**为什么 AI 杀不掉生产性 ego：**
- 困难问题需要数月级持续注意力，transformer 是 stateless 的
- AI 没有自我连续性
- **创新工作是人 在 AI Native 组织里的真正不可替代领域**

**三类工作 / 三种治理：**

| 工作类型 | 治理方式 |
|---------|---------|
| 执行类 | 全透明 + 死防御性 ego |
| 优化类 | 抑制 ego + 结构化 + 留批判空间 |
| 创新类 | 保护半成形想法 + 维护生产性 ego + 半私密不强制广播 |

**搞混了 = 执行高效 + 创新死亡**

## 访谈数据

**先锋小组（AI Native 跑通后）：**
- 写代码占比：30% → 5%
- 和 Agent 对话占比：5% → 60%
- 纯编码效率提升 10 倍
- 端到端需求交付效率提升 2-3 倍
- 3-5 人垂直功能小组（不再有产品/前端/后端边界）
- 决策权与开发权分离

**中段现状：**
- 数百条调研，所有岗位/层级提及频次最高：**"系统打通与数据整合"**
- 员工在当"人肉中间件"

## 转型代价

### 三个不可回避的问题

1. **培养断裂** — day 1 的人做什么？入门级岗位可能消失。每家公司不招 day 1 是局部最优，但整个行业不招 day 1，三五年后 senior 池枯竭——产业级灾难。

2. **蒸馏焦虑破坏转型** — 员工意识到"我说得越多被替代得越快"，关键知识开始藏匿。Harness 工作恰恰需要员工说出"哪些隐性约定需要结构化"。最优秀的人先走（外部选择最多），剩下 followers，转型失败。

3. **行业级负反馈环** — death of expertise 方向的互相加速。

### 应对方向

- 明确 AI 红利分享方式（扩展边界 vs 收缩团队）
- 真实"接住"机制（Architect 通道、跨领域 DRI）
- 诚实分类岗位形态变化
- 评价系统跟着变（"判断比执行值钱"但 KPI 还是产出量，员工不信）

## 关键判断

1. **Harness 是复利本金** — 早投入和晚投入差距是指数的。今天投在 Harness 上的工程精力 = 明天 AI Native 速度。

2. **AI Native 不是又一次 reorg** — 真正价值是让组织未来不再需要痛苦 reorg。Execution Graph 重组成本从季度级压到 week 级。

3. **Architect 激励问题** — 把"被威胁的资深工程师"转化为"被赋能的 Architect"：给身份、给权力、给资源。

4. **分辨节点类型** — 执行节点全透明 + 死防御性 ego；创新节点保护环境 + 维护生产性 ego。

5. **开始做 Agent 名册** — 你不可能治理你叫不出名字的东西。

## 相关引用

- [[harness-core-concept-20260421]] — Harness Engineering 核心概念
- [[claude-code-agentic-harness-20260421]] — Claude Code 的 Agentic Harness 架构
- [[harness-engineering-java-brownfield-20260507]] — Harness Engineering 实战：Java 工程 AI Coding 率从 25% 提升至 90%
- [[AI-DevOS]] — 面向软件工程长期自治的操作系统
- [[luo-fuli-3h-interview-zhangxiaojun-2026]] — 罗福莉访谈：AI 范式从 Chat 时代转向 Agent 时代

## 参考文献

- [1] Jack Dorsey & Roelof Botha, *From Hierarchy to Intelligence*, Block Inside, March 2026
- [2] Melvin E. Conway, *How Do Committees Invent?*, Datamation, April 1968
- [3] Frederick P. Brooks Jr., *The Mythical Man-Month*, Addison-Wesley, 1975
- [4] Ken Huang, *What is an Agentic AI Native Organization?*, Substack, February 2026
- [5] Peter Pang, *Why Your "AI-First" Strategy Is Probably Wrong*, X, April 2026
- [6] Steve Yegge, *The Anthropic Hive Mind*, Medium, February 2026
