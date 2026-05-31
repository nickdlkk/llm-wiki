---
title: "让 AI 自己做增长：基于OPC和Harness思想的自主增长系统探索"
summary: "阿里高德团队用多 Agent 架构实现 SEO 增长全流程自主化：0 人为介入，4 小时完成路书应用上线，Harness Engineering 落地实践"
source: "阿里妹（阿里巴巴）"
url: https://mp.weixin.qq.com/s/sZm-KDM7NoITchuhpbJkJQ
author: 曼行
date: 2026-05-31
tags: [harness-engineering, multi-agent, autonomous, OPC, growth, se, evaluation]
ingested: 2026-05-31
---

# 让 AI 自己做增长：基于OPC和Harness思想的自主增长系统探索

## 一句话总结

高德团队基于 Harness Engineering 思想，用多 Agent 分工 + 状态机工作流 + 独立 Evaluator 门禁，实现了从"发现增长机会"到"代码上线"的全流程 0 人为介入，4 小时跑通路书应用。

## 背景：OPC + AI Agent

**OPC** = One Person Company（一人公司）

核心问题：发现增长机会→设计方案→写代码→测试上线，每个环节都需要专业能力和大量时间。传统做法是每个链路有特定的人参与。

解决思路：借鉴"一个人用 AI 自主发现需求、全程自主开发和上线 APP"的社媒案例，在高德 PC 站 SEO 场景实践 OPC 思想，让 AI Agent 独立自主完成全流程。

## 经典 Harness Engineering（Anthropic 实践）

```
Planner → 接收 1-4 句提示 → 扩展为完整产品规格
Generator → 根据提示创建 HTML/CSS/JS 前端
Evaluator → 配备 Playwright MCP，直接与运行中页面交互、截图、研究效果
反馈循环 → 评估结论回传给 Generator 迭代
```

核心：Feedback Loop + Quality Gate，确保 AI 输出可靠、一致、可维护。

## 高德业务架构

在经典 Harness Engineering 基础上做了更细分的设计：

1. **总控 Agent（Orchestrator）**：统筹调度
2. **记忆系统**：文件形式记录各阶段产物和运行日志，上游产出自动成为下游输入
3. **专业 Agent 拆分**：保持专业的人做专业的事
4. **专业 Skill**：垂类方向上更准确，节约时间

## 核心架构：多 Agent 分工

### Planner 拆分为 3 个专业 Agent

| 原角色 | 拆分后 Agent | 职责 |
|--------|-------------|------|
| Planner | product_agent | 产品需求定义：将用户需求转化为结构化 PRD |
| Planner | design_agent | UI/UX 设计规格：前端界面、交互流程、视觉规范 |
| Planner | arch_agent | 技术架构规格：系统架构、数据模型、API 设计、Sprint 分解 |

### Builder 拆分为 2 个专业 Agent

| 原角色 | 拆分后 Agent | 职责 |
|--------|-------------|------|
| Builder | testcase_agent | 测试用例设计：生成所有 Sprint 的测试用例文档和测试代码 |
| Builder | builder_agent | 功能实现：读取测试代码理解验收标准，实现功能代码 |

### 为什么要拆分？

- 单个 Agent 职责太多会导致**上下文爆炸**，尤其是长任务中
- **强制每次启动新的 SubAgent**：避免上下文污染，实验证明同一个上下文开启多件事时记忆会混乱，实现质量快速下降

## 长任务保障机制

### 状态机流转

```
DISPATCHED → ACKED → RUNNING → SUCCEEDED / FAILED
```

- **心跳监控**：子 Agent 在 RUNNING 状态时需定期发送心跳（每 60 秒）
- **超时恢复**：TIMED_OUT（300 秒内未 ACK）或 STALLED（1200 秒内未完成）需人工或自动触发重试
- **轮询检查**：每 60 秒检查一次所有任务状态

### 进入条件（Condition）

每个 Agent 都有明确的执行前提，防止跳过必要环节：
- design_agent 的 condition：product_run_status == SUCCEEDED
- arch_agent 的 condition：design_run_status == SUCCEEDED
- testcase_agent 的 condition：contract_status == ACTIVE

### 失败处理

| 失败类型 | 最终状态 | 处理方式 |
|---------|---------|---------|
| 条件不满足 | 对应 on_failure 状态 | 终止或重试 |
| 执行失败 | FAILED | 记录失败 + 终止 |
| 超时 | TIMED_OUT | 升级人工 |
| 卡死 | STALLED | 升级人工 |
| 风控阻断 | BLOCKED | 冻结流程 |

## 评估独立性：Evaluator 拆分

评估必须和生成彻底分离——同一个 AI 既写又审容易给自己打满分。

### 6 个独立评审 Agent

| 子 Agent | 评审对象 | 评估维度 |
|---------|---------|---------|
| proposal_reviewer | growth_proposal 产出的提案 | 机会真实性、方案可行性、增长潜力、风险可控性、验收标准完整性 |
| prd_reviewer | product_agent 产出的 PRD | 用户需求完整性、产品价值清晰性、成功指标可量化、功能清单完整性、验收标准可测试性 |
| contract_reviewer | builder_agent 提交的 Sprint Contract | 结构完整性、内容清晰度、技术可行性、验收标准质量、风险管控 |
| testcase_reviewer | testcase_agent 产出的测试用例 | PRD 覆盖、断言质量、边界场景、可运行性 |
| impl_reviewer | builder_agent 的代码实现 | 功能完整性、功能通路可用性、代码质量、测试覆盖、运行时验证、架构合规性 |
| frontend_design_reviewer | design_agent 产出的前端设计 | 功能、设计质量、独创性、艺术性 |

### 三条设计原则

1. **评审与生成彻底分离**：Evaluator 只输出反馈，不改代码
2. **零信任**：Builder 说"测试通过了"，Evaluator 得自己跑一遍；Builder 说"服务已启动"，得自己验证
3. **零 Broken Feature**：Evaluator 是最后一道防线，不能因为"只差一点点"就放水

## Evaluator 自进化：Benchmark 体系

### 核心问题

如何知道 Evaluator 的能力是 60 分位还是 90 分位？

### 解法：元评估

Benchmark 评的不是"代码写得好不好"，而是"**Evaluator 评得准不准**"。

### Benchmark 数据集构建

**第一层：Code Snippets（代码片段）**
- 针对"代码规不规范"设计了 good example 和 bad example
- 从 GitHub 权威开源项目挑选优秀代码片段和典型问题代码
- Golden Answer 标注每处违规的 ESLint 规则编号、严重级别、修复方案

**第二层：Project（完整项目）**
- 覆盖三种复杂度：todo（简单）、博客（中等）、电商（复杂）
- 负面组按 OWASP Top 10、CWE 通用缺陷列表植入 bug
- bug 分为简单（1分）、中等（2分）、复杂（3分）

**零注释、零提示**：完全模拟真实评审，评测的是"发现问题的能力"而非"读取注释的能力"。

### 三层评审流程 + 快速失败优先

| 层次 | 评估内容 | 验证方式 | 退出条件 |
|------|---------|---------|---------|
| 第一层 | TypeScript 类型安全、命名规范、错误处理 | 纯静态分析（ESLint + tsc） | 编译失败 → 立即终止 |
| 第二层 | 架构合规性、安全实践、SQL 注入防护 | 深层语义理解 + 规则匹配 | 安全漏洞 → 终止并标注风险 |
| 第三层 | Playwright E2E、单元测试、运行时验证 | 真正启动项目执行 | P0 测试失败 → 终止 |

**快速失败 6 步执行流程**：

1. **环境变量检查**（<1秒）：.env.local 是否存在
2. **依赖安装检查**（5-10秒）：node_modules 是否存在
3. **编译验证**（10-30秒）：TypeScript 编译
4. **开发服务器启动**（15-30秒）：npm run dev
5. **静态分析**（30-60秒）：ESLint + 安全扫描
6. **动态验证**（2-5分钟）：Playwright E2E

**实战案例**：第一次跑完整流程 4 分钟，发现所有测试失败——因为 Builder 没配置 .env.local。引入快速失败后，Step 1 就拦住了，1 秒退回 Builder。

### 评分体系

**代码片段模式（code_snippet）**：纯静态分析，满分 100
- 规则遵从率（40分）：找全了没有
- 严重度加权分（30分）：分级分对了没有
- 一致性得分（20分）：分类准不准
- 报告质量（10分）：能不能指导修复

**完整项目模式（full_runtime）**：静态 + 动态，满分 100
- 静态分析 40 分（Bug 检出率 15、精确度 10、安全审计 10、结论校准 5）
- 动态验证 60 分（测试验证 20、构建验证 15、运行时验证 15、性能验证 10）
- **能力系数**：没跑测试/构建/启动服务，系数打折

### 三轮 Benchmark 优化结果

| 轮次 | 均分 | 精确匹配 | 误报（负面/正面） | CRITICAL 漏检 | 级别误判 | 状态 |
|------|------|---------|-----------------|--------------|---------|------|
| 第一轮 | 64.5 | 25% | ~0 / 5 | 0 | 2 | 未通过 |
| 第二轮 | 67.5 | 42% | ~12 / 5 | 2 | 3 | 未通过 |
| 第三轮 | 83.4 | 78% | 1 / 0 | 0 | 0 | 通过 |

核心：问题定位靠数据对比，改进方向靠根因分析，效果验证靠分数复测。

## 踩坑与解决方案

### 1. 构建稳定可运行环境

- **环境工具化**：SDK、底层框架、业务沉淀的工具/组件都要面向 AI 工具化。案例：builder 宣称自己开发完了加签算法但无法通过，封装好加签算法后调用正常
- **Skill 接入**：接入高德 Skill 和 API 后，成功率从 10%-20% 提升到接近 100%

### 2. 评审报告标准化

- 包含 CRITICAL/MAJOR/MINOR 分类，按严重程度依次修复
- 检测到阻塞性问题可直接反馈给 builder，跳过后续测试 case

### 3. 端到端自动化的工程化难度被严重低估

四个具体坑：
1. **状态管理**：成功/失败/部分成功的判断逻辑远比想象中复杂
2. **超时和资源管理**：Agent 调用有延迟，串联后任务可能长达十几分钟
3. **环境一致性**：本地开发正常但云端构建失败
4. **可追溯日志**：需要知道每次失败发生在哪里、什么时间、为什么

### 4. 保留人工线上部署确认

自动部署上线涉及部署前预检查、部署后健康检查、灰度发布策略、回滚触发条件，每个都是独立工程问题。所以**线上环境保留人工确认环节**——人不是依赖概率的，而是真实执行全流程。

### 5. 重新理解"完全无人干预"

完全无人干预不是 0/1 状态。真正有价值的是把人工干预的频率和成本降到足够低——低到一个人能同时监护几十个并行任务，只在少数关键节点介入。**不是替代人，而是把一个人的产能放大到几十倍。**

### 6. 小步快跑，保证上一轮正常

不要追求一步到位的全自动化。每次循环只解决一个 Agent 的某个点的问题。每个版本保证最起码的通路，再在当前版本上迭代下一个版本。

### 7. 长链路稳定性仍然脆弱

从发现机会到代码上线经过 8-10 个节点，即使每个节点 90% 成功率，端到端通过率也会很低。目前靠重试机制和门禁回退兜底，基本需要 1-3 次不等的循环才能修掉 P0 问题。

## 未来方向

- **数据集完善**：目前只覆盖项目和代码片段级别，Product/Design/Arch/Testcase Agent 缺少开源数据集，需要自建并标注
- **OPC + AI Agent 的想象空间**：PC 网站场景（模型擅长、实现明确、反馈周期短、试错成本低）已经是 AI 自主系统的理想试验田；决策后果严重、反馈周期长、深度领域知识、安全合规极高的场景还需要更成熟的 Harness 框架

## 相关资料

- [[Harness Engineering]] — AI 工程第三次重心迁移
- [[Four-Layer Feedback Loop]] — 编译→单测→e2e→CI 四层反馈闭环
- [[gstack/benchmark]] — Garry Tan 的 Claude Code 技能包，生产级 Harness 参考
- [[se-and-llm-harness]] — 软件工程实践与 LLM Harness 的深层关联

## 原始来源

^[raw/articles/gaode-autonomous-growth-20260531.md] 微信公众号原文