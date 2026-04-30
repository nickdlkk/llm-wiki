---
kind: raw
type: forum-posts
source: V2EX 帖子原始内容
date: 2026-04-16
---

# V2EX Harness 工程帖子原始内容

## 帖子1: 如何实践 Harness 工程？
- URL: https://www.v2ex.com/t/1205969
- 作者: kenshinhu

看了好几个内容：OpenAI Harness Engineering 官方页面、walkinglabs/learn-harness-engineering（中文教程）、baoyu.io 宝玉的争议文章。该怎样实践 Harness？

有别于 specs、vibe 这种，有什么现成项目可以尝试 Harness 工程？

对于后端 API 服务理解怎样做（有需求让 AI 处理，提交 PR 时做单元测试，加定时器做全量测试），但前端项目怎样做？带着好奇心请教各位。

---

## 帖子2: 写了三个月 Agent Harness，我终于敢让 Claude Code 全自动写代码了
- URL: https://www.v2ex.com/t/1205752
- 作者: fennu2333

### 核心观点

作者用 Claude Code 做项目开发，之前工作流是在 Asana 和终端之间来回搬运文本，崩溃后决定做开源项目 Chorus（给 Agent 一个它自己能用的任务管理器）。

**三个月的演进过程：**

**v0.1（2月底）：先让 Agent 自己看到活**
- 管道跑通：Idea -> Proposal -> Task -> Execute -> Verify
- 参考 AWS AI-DLC（AI-Driven Development Lifecycle）
- 加了"反转对话"设计：人只抛粗糙想法，PM Agent 主动提问聊清需求，自己出方案、拆任务，审批后 Developer Agent 去领活
- Claude Code 插件自动注入当前项目有哪些活要干、哪些任务被分配

**v0.4（3月中）：Agent 太容易"自以为做完了"**
- 问题：Agent 说"做完了"可信度约五成（代码写了但没测、主流程通了但边界没覆盖）
- 加了两个机制：
  1. 验收标准 checklist：每个 Task 有独立 AC 列表，Agent 做完必须逐条自检 pass/fail
  2. 依赖强制执行：上游任务没验收通过，下游任务 API 不返回

**v0.6（4月初）：让 Agent 审 Agent**
- 问题：谁做验收？还是人，成为瓶颈
- 利用 Claude Code 插件机制定义独立的 reviewer agent
  - proposal-reviewer：审方案（接口设计、任务拆分、AC 能不能测）
  - task-reviewer：审代码（实现有没有满足 AC）
- 审查记录永久存档，审不过就打回附具体问题，Agent 改完再交
- 最多三轮还过不了才上报人类

**v0.6.1（上周）：/yolo，全串起来**
- Elaboration 把需求聊透，Proposal 把方案想清楚，Reviewer 把质量守住
- /yolo：一句话进去，Agent 自己走完整条管道，中间不问你一句话
- Ctrl+C 随时能断，已创建任务都持久化
- 某个 Task 反复过不了审，标记需要人工介入然后继续推进其他 Task

**实际效果：**
- 调试思路变了：直接翻 Elaboration 记录和 Proposal 审查历史就能定位哪个假设出了偏差
- 时间释放了：主要在想下一个 feature 做什么，reviewer 标记"需要人工介入"时才去看一眼
- 中等复杂度的 feature（3-8个 task）成功率比预期高

**项目信息：**
- 开源，AGPL-3.0，Next.js + Prisma + PostgreSQL
- GitHub: https://github.com/Chorus-AIDLC/Chorus
- Demo: https://chorus-ai.dev/zh/blog/chorus-v0.6.1-release/

---

## 帖子3: 用 AgentsMesh 造 AgentsMesh：一个人 52 天的 Harness Engineering 实践
- URL: https://www.v2ex.com/t/1196036
- 作者: yishuiliunian

### 核心观点

52天，600次提交，965,687行代码吞吐，现存356,220行代码，一个人。用 Harness Engineering 方式建造了 AgentsMesh（Harness Engineering 的工具）。

**核心洞察：工程环境决定 Agent 的输出上限**

**1. 分层架构，让 Agent 知道在哪里改**
- 严格 DDD 分层：domain 层只有数据结构，service 层只有业务逻辑，handler 层只有 HTTP 格式转换
- 22 个域模块，边界清晰，每个模块 interface.go 明确定义对外契约
- Agent 需要添加新功能时：数据结构放 domain，业务规则放 service，路由放 handler

**2. 目录结构即文档**
- 跨端命名完全对齐（Loop 例子：backend/internal/domain/loop/ = 数据结构，backend/internal/service/loop/ = 业务逻辑，web/src/components/loops/ = 前端组件）
- backend 16 个 domain 模块和 service 层 1:1 镜像；web components 按产品功能分区，和 backend domain 命名对齐

**3. 技术债务会被 Agent 指数级放大**
- Agent 会把临时性妥协的模式学走并复用，不是偶发是系统性的
- 人工工程师知道"绕开坑"，Agent 不会做这个判断
- 意味着仓库里代码质量信号比人写代码时重要得多
- 实际应对：中间多次停下来专门清理技术债务，不发新功能，只做重构

**4. 强类型作为编译期质量闸**
- Go + TypeScript + Proto
- Agent 生成了签名不匹配的函数？编译失败
- Agent 修改了 API 格式忘了更新类型定义？TypeScript 直接报错
- 反馈环路越短，Agent 迭代效率越高

**5. 四层反馈闭环**
- 第一层：编译（Air 热重载，Go 1秒内重启，TS 类型错误实时标注）
- 第二层：单测（700+ 测试覆盖 domain 和 service 层，5分钟内知道有没有引入回归）
- 第三层：e2e（端到端验证真实功能路径）
- 第四层：CI pipeline（每个 PR 自动跑全量测试、lint、type-check、多平台构建验证）

**6. 工作树原生支持并行**
- dev.sh 根据 Git worktree 名自动计算端口偏移，每个工作树分配独立端口区间
- 多个 Agent 在不同工作树上同时工作，环境完全隔离

**7. 代码库就是 Agent 的上下文**
- 仓库即 context：不需要刻意做 Context Engineering，不需要单独搭 RAG，不需要维护额外的 memory 文件
- 分层架构告诉 Agent 该在哪里改；目录结构告诉 Agent 该找哪个文件；技术债务清洁程度决定 Agent 学到好模式还是坏模式；测试密度决定 Agent 有多大胆量重构；强类型决定 Agent 的错误能在多早被发现

**8. 认知带宽是真实的工程约束**
- 三个 worktree 同时开着，三个 Agent 在跑，在它们之间切换做决策
- 加第四个，决策质量明显下降
- 日均 5 万行吞吐量不是工具限制，是人类认知带宽的自然上限
- 突破方式：用委托换规模 → Autopilot 模式

**三个工程原语（被真实的工程问题逼出来的）：**

1. **隔离（Isolation）**：每个 Agent 需要自己独立的工作空间。AgentsMesh 用 Pod 实现：每个 Agent 运行在独立的 Git worktree 和沙箱里。隔离同时也意味着内聚，在独立 Pod 环境中把 Agent 运行需要的全部上下文准备好（Repo、Skills、MCP and more）

2. **分解（Decomposition）**：Agent 不擅长处理"帮我搞这个代码库"，擅长的是：你拥有这个范围，这是验收标准，这是完成定义。AgentsMesh 提供了两种抽象：
   - Ticket：一次性的工作项（功能开发、Bug修复、重构），有完整看板状态流转和 MR 关联
   - Loop：周期性的自动化任务（每日测试、定时构建、代码质量扫描），用 Cron 表达式调度，每次运行留下独立 LoopRun 记录

3. **协调（Coordination）**：
   - Channel：多个 Pod 在同一个协作空间里共享消息、决策和文档，是 Supervisor Agent 和 Worker Agent 形成协作结构的基础
   - Binding：两个 Pod 之间点对点的权限授权（terminal:read 让感知状态，terminal:write 让直接控制执行）

**自举验证：**
AgentsMesh 的核心主张：AI Agent 可以在有结构的 Harness 下协作完成复杂工程任务。用 AgentsMesh 建造了 AgentsMesh。52天，965,687行代码吞吐，现存356,220行生产代码，600次提交，一个作者。

**关于试错成本坍缩：**
当试错成本接近零，旧的工程直觉（先设计再建造）变成了负担。AgentsMesh 的 Relay 架构不是设计出来的，是被生产环境打出来的。AI 改变的不是写代码的速度，是整个工程过程的成本结构。
